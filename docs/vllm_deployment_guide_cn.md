# 🚀 MiniMax-Text-01 模型 vLLM 部署指南

## 📖 简介

我们推荐使用 [vLLM](https://docs.vllm.ai/en/latest/) 来部署 MiniMax-Text-01 模型。经过我们的测试，vLLM 在部署 MiniMax-Text-01 时表现出色，具有以下特点：

- 🔥 卓越的服务吞吐量性能
- ⚡ 高效智能的内存管理机制
- 📦 强大的批量请求处理能力
- ⚙️ 深度优化的底层性能

MiniMax-Text-01 模型可在单台配备8个H800或8个H20 GPU的服务器上高效运行。在硬件配置方面，搭载8个H800 GPU的服务器可处理长达200万token的上下文输入，而配备8个H20 GPU的服务器则能够支持高达500万token的超长上下文处理能力。

## 💾 获取 MiniMax-Text-01 模型

您可以从我们的官方 HuggingFace 仓库下载模型：[MiniMax-Text-01](https://huggingface.co/MiniMaxAI/MiniMax-Text-01)

下载命令：
```
pip install -U huggingface-hub
huggingface-cli download MiniMaxAI/MiniMax-Text-01

# 如果遇到网络问题，可以设置代理
export HF_ENDPOINT=https://hf-mirror.com
```

或者使用 git 下载：

```bash
git lfs install
git clone https://huggingface.co/MiniMaxAI/MiniMax-Text-01
```

⚠️ **重要提示**：请确保系统已安装 [Git LFS](https://git-lfs.github.com/)，这对于完整下载模型权重文件是必需的。

## 🛠️ 部署方案

### 方案一：使用 Docker 部署（推荐）

为确保部署环境的一致性和稳定性，我们推荐使用 Docker 进行部署。

⚠️ **版本要求**：
- MiniMax-Text-01 模型需要 vLLM 0.8.3 或更高版本才能获得完整支持
- 如果您使用的 Docker 镜像中的 vLLM 版本低于 0.8.3（在编写本文档时该版本尚未发布），您需要：
  1. 更新到最新的 vLLM 代码
  2. 从源码重新编译 vLLM，按照"常见问题"部分中解决方案二的说明进行编译

1. 获取容器镜像：
```bash
docker pull vllm/vllm-openai:v0.7.1
```

2. 运行容器：
```bash
# 设置环境变量
IMAGE=vllm/vllm-openai:v0.7.1
MODEL_DIR=<模型存放路径>
CODE_DIR=<代码路径>
NAME=MiniMaxImage

# Docker运行配置
DOCKER_RUN_CMD="--network=host --privileged --ipc=host --ulimit memlock=-1 --shm-size=2gb --rm --gpus all --ulimit stack=67108864"

# 启动容器
sudo docker run -it \
    -v $MODEL_DIR:$MODEL_DIR \
    -v $CODE_DIR:$CODE_DIR \
    --name $NAME \
    $DOCKER_RUN_CMD \
    $IMAGE /bin/bash
```


### 方案二：直接安装 vLLM

如果您的环境满足以下要求：

- CUDA 12.1
- PyTorch 2.1

可以直接安装 vLLM

安装命令：
```bash
pip install vllm
```

💡 如果您使用其他环境配置，请参考 [vLLM 安装指南](https://docs.vllm.ai/en/latest/getting_started/installation.html)

## 🚀 启动服务

### 启动 MiniMax-Text-01 服务

```bash
export SAFETENSORS_FAST_GPU=1
export VLLM_USE_V1=0

python3 -m vllm.entrypoints.api_server \
--model <模型存放路径> \
--tensor-parallel-size 8 \
--trust-remote-code \
--quantization experts_int8  \
--max_model_len 4096 \
--dtype bfloat16
```

### API 调用示例

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

## ❗ 常见问题

### 模块加载问题
如果遇到以下错误：
```
import vllm._C  # noqa
ModuleNotFoundError: No module named 'vllm._C'
```

或

```
当前并不支持 MiniMax-Text-01 模型
```

我们提供两种解决方案：

#### 解决方案一：复制依赖文件
```bash
cd <工作目录>
git clone https://github.com/vllm-project/vllm.git
cd vllm
cp /usr/local/lib/python3.12/dist-packages/vllm/*.so vllm 
cp -r /usr/local/lib/python3.12/dist-packages/vllm/vllm_flash_attn/* vllm/vllm_flash_attn
```

#### 解决方案二：从源码安装
```bash
cd <工作目录>
git clone https://github.com/vllm-project/vllm.git

cd vllm/
pip install -e .
```

## 📮 获取支持

如果您在部署 MiniMax-Text-01 过程中遇到任何问题：
- 请查看我们的官方文档
- 通过官方渠道联系我们的技术支持团队
- 在我们的 GitHub 仓库提交 [Issue](https://github.com/MiniMaxAI/MiniMax-Text-01/issues)

我们会持续优化 MiniMax-Text-01 的部署体验，欢迎您的反馈！


