---
title: 将 transformers 模型转换为 ONNX 格式
date: 2021-12-08 15:29:04
categories:
- NLP
- transformers
tags:
- transformers
- pytorch
index_img: /img/hf.png
banner_img: 
---

# 将 transformers 模型转换为 ONNX 格式

然后就可以优化模型，加速推理了。这里以 BERT 为例。

## 环境

torch == 1.8.0

transformers == 4.12.5

onnx == 1.10.2

onnxruntime == 1.10.0

## 步骤

### 转换 ONNX 文件

运行 transformers 官方提供的脚本 [convert_graph_to_onnx.py](https://github.com/huggingface/transformers/blob/master/src/transformers/convert_graph_to_onnx.py) ，直接得到转换好格式的 ONNX 模型。（或者直接 import 进来，调用里面的 convert() 方法。）

注意 pipeline 要跟自己的模型任务对应上，否则无法正常工作。例如这里我用的是 `BertForSequenceClassification` ，pipeline 就选 `sentiment-analysis` （等价于 `TextClassificationPipeline`）

```shell
python -m transformers.convert_graph_to_onnx --pipeline sentiment-analysis --model "./models/chinese-bert-wwm-ext_2021-12-06_11-33-24" --framework pt "./models/onnx/chinese-bert-wwm-ext/model.onnx"
```

注意模型转换时控制台的打印信息，确定模型的输入输出跟原来的 forward() 函数一致。`--model` 参数就是用 save_pretrained() 方法保存的模型。我的目录结构如下：

```
models
	| chinese-bert-wwm-ext_2021-12-06_11-33-24
		| added_tokens.json
		| config.json
		| pytorch_model.bin
		| tokenizer.json
		| tokenizer_config.json
		| special_tokens_map.json
		| vocab.txt
	| onnx
		| chinese-bert-wwm-ext
			| model.onnx
```

### 利用 onnx session 进行推理

```python
import onnxruntime as ort
from transformers.models.bert import BertTokenizerFast

model_path = "./models/chinese-bert-wwm-ext_2021-12-06_11-33-24"
onnx_path = "./models/onnx/chinese-bert-wwm-ext/model.onnx"
tokenizer = BertTokenizerFast.from_pretrained(model_path)

ort_session = ort.InferenceSession(onnx_path)

inputs = tokenizer(["hello"], ["motherfucker"])
outputs = ort_session.run(None, dict(inputs))

# outputs: [array([[ 3.5934877, -3.5006552]], dtype=float32)]
```

run() 方法中需要传入对应名字的参数。

## 参考

pipeline 有哪些：[Pipelines](https://huggingface.co/docs/transformers/master/en/main_classes/pipelines#pipelines) 

哪些 pipeline 可以直接用官方脚本转换（直接看 help 信息）：

```python
SUPPORTED_PIPELINES = [
    "feature-extraction",
    "ner",
    "sentiment-analysis",
    "fill-mask",
    "question-answering",
    "text-generation",
    "translation_en_to_fr",
    "translation_en_to_de",
    "translation_en_to_ro",
]
```

transformers 官方文档：[Exporting transformers models](https://huggingface.co/docs/transformers/master/en/serialization#exporting-transformers-models)

官方博客：[Accelerate your NLP pipelines using Hugging Face Transformers and ONNX Runtime](https://medium.com/microsoftazure/accelerate-your-nlp-pipelines-using-hugging-face-transformers-and-onnx-runtime-2443578f4333)

一个也许有用的 GitHub issue：[Onnx converted model has its output shape modified when compared to original (finetuned) model](https://github.com/huggingface/transformers/issues/4825)



