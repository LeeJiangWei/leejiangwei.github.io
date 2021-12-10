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

# 将 transformers 模型转换为 ONNX 格式并用 TensorRT 加速

然后就可以优化模型，加速推理了。这里以 BERT 为例。

## 转换 ONNX 文件

运行 transformers 官方提供的脚本 [convert_graph_to_onnx.py](https://github.com/huggingface/transformers/blob/master/src/transformers/convert_graph_to_onnx.py) ，直接得到转换好格式的 ONNX 模型。（或者直接 import 进来，调用里面的 convert() 方法。）

注意 pipeline 要跟自己的模型任务对应上，否则无法正常工作。例如这里我用的是 `BertForSequenceClassification` ，pipeline 就选 `sentiment-analysis` （等价于 `TextClassificationPipeline`）

```shell
python -m transformers.convert_graph_to_onnx --pipeline sentiment-analysis --framework pt --model "./models/chinese-bert-wwm-ext_2021-12-06_11-33-24" --check-loading --quantize "./models/onnx/chinese-bert-wwm-ext/model.onnx"
```

注意模型转换时控制台的打印信息，确定模型的输入输出跟原来的 forward() 函数一致。`--model` 参数就是用 save_pretrained() 方法保存的模型。指定 `--quantize` 参数会生成 optimize 和 quantize 后的模型。脚本运行完后，我的目录结构如下：

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
			| model-optimized.onnx
			| model-optimized-quantized.onnx
```

## 在 CPU 上进行推理

至此已经可以用 ONNX 进行推理了。

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

## 下载并安装 TensorRT

ONNX 本身也会加速推理，但是可以利用 TensorRT 进一步加速 GPU 上的推理速度。CPU 上也有类似的加速框架 openvino。这些是特定硬件平台（nvidia 和 intel）自家的加速引擎。

去英伟达官网 [NVIDIA TensorRT](https://developer.nvidia.com/zh-cn/tensorrt) 下载 TensorRT。

**重要**：TensorRT 版本要和 CUDA、cudnn、onnx runtime 的版本都对应上，见 [Requirements](https://onnxruntime.ai/docs/execution-providers/TensorRT-ExecutionProvider.html#requirements) 。一个版本号会有两种版本，EA (early access) 和 GA (general availability)，选 GA 就好。这里我的版本是 8.0 GA Update 1，这个版本似乎正好还有专门为 transformers 模型的优化。

> Ensure that you have the following dependencies installed.
>
> - [CUDA 10.2](https://docs.nvidia.com/cuda/archive/10.2/index.html), [11.0 update 1](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#cuda-whats-new-11Upd1), [11.1 update 1](https://developer.nvidia.com/cuda-toolkit-archive), [11.2 update 2](https://developer.nvidia.com/cuda-toolkit-archive), [11.3 update 1](https://developer.nvidia.com/cuda-toolkit-archive), or [11.4 update 2](https://developer.nvidia.com/cuda-toolkit-archive)
> - [cuDNN 8.2.1](https://docs.nvidia.com/deeplearning/cudnn/release-notes/rel_8.html#rel-821)

Windows 平台下载下来的是一个 zip 文件，安装说明见 [4.7. Zip File Installation](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html#installing-zip) 。实际就是把 lib 目录下的一堆 dll 文件扔进 CUDA 的 bin 目录下，然后 pip install 几个本地的包。安装完成后尝试 import tensorrt 验证一下是否安装成功。（官方说是用 visual studio 编译几个示例项目）

## 在 GPU 上进行推理

代码跟之前几乎没区别，就是添加了个 providers 参数。也可以不用 TensorRT 只用 CUDA，把 `TensorrtExecutionProvider` 去掉即可。

```python
import onnxruntime as ort
from transformers.models.bert import BertTokenizerFast

model_path = "./models/chinese-bert-wwm-ext_2021-12-06_11-33-24"
onnx_path = "./models/onnx/chinese-bert-wwm-ext/model.onnx"
tokenizer = BertTokenizerFast.from_pretrained(model_path)

ort_session = ort.InferenceSession(onnx_path, providers=['TensorrtExecutionProvider', 'CUDAExecutionProvider'])
# ort_session = ort.InferenceSession(onnx_path, providers=['CUDAExecutionProvider'])  # CUDA only
# ort_session = ort.InferenceSession(onnx_path, providers=['CPUExecutionProvider'])  # CPU

inputs = tokenizer(["hello"], ["motherfucker"])
outputs = ort_session.run(None, dict(inputs))

# outputs: [array([[ 3.5934877, -3.5006552]], dtype=float32)]
```

### 报警告

实际使用中，不报错误就算成功，但是可能会报如下警告：

```
2021-12-10 15:13:31.6350799 [W:onnxruntime:Default, tensorrt_execution_provider.h:53 onnxruntime::TensorrtLogger::log] [2021-12-10 07:13:31 WARNING] D:\a\_work\1\s\cmake\external\onnx-tensorrt\onnx2trt_utils.cpp:364: Your ONNX model has been generated with INT64 weights, while TensorRT does not natively support INT64. Attempting to cast down to INT32.

2021-12-10 15:13:50.2068278 [W:onnxruntime:Default, tensorrt_execution_provider.h:53 onnxruntime::TensorrtLogger::log] [2021-12-10 07:13:50 WARNING] TensorRT was linked against cuBLAS/cuBLAS LT 11.6.3 but loaded cuBLAS/cuBLAS LT 11.3.0
2021-12-10 15:13:50.2080023 [W:onnxruntime:Default, tensorrt_execution_provider.h:53 onnxruntime::TensorrtLogger::log] [2021-12-10 07:13:50 WARNING] TensorRT was linked against cuDNN 8.2.1 but loaded cuDNN 8.0.5
```

只要不报错误，无视即可，实际上不影响性能。

### TensorRT 加载、推理速度比 CPU 还慢

正常现象，跟 TensorRT 本身的机制有关，加载模型、第一次推理速度会特别慢，但是后面的就会很快了。参见：https://github.com/microsoft/onnxruntime/issues/7230#issuecomment-814619248

参考时间差异（batch_size = 1）：

```python
Load time:  14.695685863494873
Run time:  6.2073938846588135
Second run time:  0.23337483406066895
```

## 环境要求

### python

torch == 1.8.0

transformers == 4.12.5

onnx == 1.10.2

onnxruntime-gpu == 1.10.0

### system

CUDA：11.1.1_456.81_win10

cudnn：11.3-windows-x64-v8.2.1.32

TensorRT：8.2.1.8

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

PyTorch [torch.onnx](https://pytorch.org/docs/stable/onnx.html#)

一个也许有用的 GitHub issue：[Onnx converted model has its output shape modified when compared to original (finetuned) model](https://github.com/huggingface/transformers/issues/4825)

TensorRT 安装指南 [Install Guide](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html)

ONNX runtime 对 TensorRT 的支持：[TensorRT Execution Provider](https://onnxruntime.ai/docs/execution-providers/TensorRT-ExecutionProvider.html)  其中，对于 CUDA 版本要求为 11.4 的，11.x都可以兼容。
