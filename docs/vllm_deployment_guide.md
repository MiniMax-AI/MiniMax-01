# 🚀 MiniMax-Text-01 Model vLLM Deployment Guide

[VLLM中文版部署指南](./vllm_deployment_guide_cn.md)

## 📖 Introduction

We recommend using [vLLM](https://docs.vllm.ai/en/latest/) to deploy the MiniMax-Text-01 model. Based on our testing, vLLM performs excellently when deploying MiniMax-Text-01, with the following features:

- 🔥 Outstanding service throughput performance
- ⚡ Efficient and intelligent memory management
- 📦 Powerful batch request processing capability
- ⚙️ Deeply optimized underlying performance

The MiniMax-Text-01 model can run efficiently on a single server equipped with 8 H800 or 8 H20 GPUs. In terms of hardware configuration, a server with 8 H800 GPUs can process context inputs up to 2 million tokens, while a server equipped with 8 H20 GPUs can support ultra-long context processing capabilities of up to 5 million tokens.

## 💾 Obtaining the MiniMax-Text-01 Model

You can download the model from our official HuggingFace repository: [MiniMax-Text-01](https://huggingface.co/MiniMaxAI/MiniMax-Text-01)

Download command:
```
pip install -U huggingface-hub
huggingface-cli download MiniMaxAI/MiniMax-Text-01

# If you encounter network issues, you can set a proxy
export HF_ENDPOINT=https://hf-mirror.com
```

Or download using git:

```bash
git lfs install
git clone https://huggingface.co/MiniMaxAI/MiniMax-Text-01
```

⚠️ **Important Note**: Please ensure that [Git LFS](https://git-lfs.github.com/) is installed on your system, which is necessary for completely downloading the model weight files.

## 🛠️ Deployment Options

### Option 1: Deploy Using Docker (Recommended)

To ensure consistency and stability of the deployment environment, we recommend using Docker for deployment.

⚠️ **Version Requirements**: 
- The MiniMax-Text-01 model requires vLLM version 0.8.3 or later for full support
- If you are using a Docker image with vLLM version lower than 0.8.3 (which was not yet released at the time of writing this document), you will need to:
  1. Update to the latest vLLM code
  2. Recompile vLLM from source. Follow the compilation instructions in Solution 2 of the Common Issues section

1. Get the container image:
```bash
docker pull vllm/vllm-openai:v0.7.1
```

2. Run the container:
```bash
# Set environment variables
IMAGE=vllm/vllm-openai:v0.7.1
MODEL_DIR=<model storage path>
CODE_DIR=<code path>
NAME=MiniMaxImage

# Docker run configuration
DOCKER_RUN_CMD="--network=host --privileged --ipc=host --ulimit memlock=-1 --shm-size=2gb --rm --gpus all --ulimit stack=67108864"

# Start the container
sudo docker run -it \
    -v $MODEL_DIR:$MODEL_DIR \
    -v $CODE_DIR:$CODE_DIR \
    --name $NAME \
    $DOCKER_RUN_CMD \
    $IMAGE /bin/bash
```


### Option 2: Direct Installation of vLLM

If your environment meets the following requirements:

- CUDA 12.1
- PyTorch 2.1

You can directly install vLLM

Installation command:
```bash
pip install vllm
```

💡 If you are using other environment configurations, please refer to the [vLLM Installation Guide](https://docs.vllm.ai/en/latest/getting_started/installation.html)

## 🚀 Starting the Service

### Launch MiniMax-Text-01 Service

```bash
export SAFETENSORS_FAST_GPU=1
export VLLM_USE_V1=0

python3 -m vllm.entrypoints.api_server \
--model <model storage path> \
--tensor-parallel-size 8 \
--trust-remote-code \
--quantization experts_int8  \
--max_model_len 4096 \
--dtype bfloat16
```

### API Call Example

```bash
curl -X POST http://localhost:8000/generate \
    -H "Content-Type: application/json" \
    -d '{
        "prompt": "Hello, world!",
        "temperature": 1,
        "top_p": 1e-05,
        "max_tokens": 100
    }'
```

## ❗ Common Issues

### Module Loading Problems
If you encounter the following error:
```
import vllm._C  # noqa
ModuleNotFoundError: No module named 'vllm._C'
```

Or

```
MiniMax-Text-01 model is not currently supported
```

We provide two solutions:

#### Solution 1: Copy Dependency Files
```bash
cd <working directory>
git clone https://github.com/vllm-project/vllm.git
cd vllm
cp /usr/local/lib/python3.12/dist-packages/vllm/*.so vllm 
cp -r /usr/local/lib/python3.12/dist-packages/vllm/vllm_flash_attn/* vllm/vllm_flash_attn
```

#### Solution 2: Install from Source
```bash
cd <working directory>
git clone https://github.com/vllm-project/vllm.git

cd vllm/
pip install -e .
```

## 📮 Getting Support

If you encounter any issues while deploying MiniMax-Text-01:
- Please check our official documentation
- Contact our technical support team through official channels
- Submit an [Issue](https://github.com/MiniMaxAI/MiniMax-Text-01/issues) on our GitHub repository

We will continuously optimize the deployment experience of MiniMax-Text-01 and welcome your feedback!


