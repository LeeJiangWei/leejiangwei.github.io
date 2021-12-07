---
title: 对 HuggingFace Transformers 模型进行后训练
date: 2021-12-07 18:46:04
categories:
- NLP
- transformers
tags:
- transformers
- pytorch
index_img: /img/hf.png
banner_img: 
---

# 对 HuggingFace Transformers 模型进行预训练 (post-train / further pretrain)

这几天需要对 BERT 进行进一步的预训练，而且是 MLM 和 NSP 两个任务一起。但是 transformers 官方只提供了 MLM 任务的训练例子。（可能是因为 RoBERTa 原论文中认为 NSP 任务没用）

后来终于在 stackoverflow 上找到了[解决方法](https://stackoverflow.com/questions/65646925/how-to-train-bert-from-scratch-on-a-new-domain-for-both-mlm-and-nsp)。在自己的数据集上试验有效后记录于此。

## 产生任务对应的标注数据

### 使用 `TextDatasetForNextSentencePrediction` 构造 NSP 任务 Dataset

文档：官方文档没写，需要阅读[源代码](https://github.com/huggingface/transformers/blob/9f72e8f4e1e767c5f608dd135199e592255b8a69/src/transformers/data/datasets/language_modeling.py)。

数据格式：一行一句话，不同的文档之间空一行。

> 1. One sentence per line. These should ideally be actual sentences, not entire paragraphs or arbitrary spans of text. (Because we use the sentence boundaries for the "next sentence prediction" task).
> 2. Blank lines between documents. Document boundaries are needed so that the "next sentence prediction" task doesn't span between documents.

输入例子：

```tex
I am very happy.
Here is the second sentence.

A new document.
```

### 使用 `DataCollatorForLanguageModeling` 对数据进行 MLM 标注

文档：[DataCollatorForLanguageModeling](https://huggingface.co/docs/transformers/main_classes/data_collator#transformers.DataCollatorForLanguageModeling)

注：无法使用 WWM （[DataCollatorForWholeWordMask](https://huggingface.co/docs/transformers/main_classes/data_collator#transformers.DataCollatorForWholeWordMask)），因为它的实现中会丢弃掉 NSP Dataset 返回的一些值，只保留了 `input_ids` 和 `labels` 。

## 代码

模型（使用专门的 [BertForPreTraining](https://huggingface.co/docs/transformers/master/en/model_doc/bert#transformers.BertForPreTraining) 预训练类）、分词器定义

```python
from transformers.models.bert import BertTokenizerFast, BertForPreTraining

tokenizer = BertTokenizerFast.from_pretrained(model_name)
model = BertForPreTraining.from_pretrained(model_name)
```

给分词器加入新的 token 并 resize 词表

```python
tokenizer.add_tokens(["[name]", "[gender]"])
tokenizer.add_special_tokens({"eos_token": "[EOT]"})
model.resize_token_embeddings(len(tokenizer))
```

定义 dataset 和 collator。dataset 返回的样本会以字典形式传入到模型的 `__call__()` 方法（forward）中，因此需要检查是否构建了正确的数据集。

```python
from transformers.data import DataCollatorForLanguageModeling
from transformers.data.datasets import TextDatasetForNextSentencePrediction

dataset = TextDatasetForNextSentencePrediction(tokenizer, "./dataset_v2/pretrain.txt", block_size=256)
collator = DataCollatorForLanguageModeling(tokenizer, mlm=True)
```

定义 [TrainingArgument](https://huggingface.co/docs/transformers/master/en/main_classes/trainer#transformers.TrainingArguments) 以及 [Trainer](https://huggingface.co/docs/transformers/master/en/main_classes/trainer#transformers.Trainer) 

```python
from transformers.trainer import TrainingArguments, Trainer

training_args = TrainingArguments(
    output_dir=out_dir,
    overwrite_output_dir=True,
    num_train_epochs=num_epochs,
    per_device_train_batch_size=64,
    save_strategy="epoch"
)

trainer = Trainer(
    model=model,
    args=training_args,
    data_collator=collator,
    train_dataset=dataset
)
trainer.train()
```

最后保存模型以及分词器就完成了。

```python
tokenizer.save_pretrained(out_dir)
trainer.save_model()
```

