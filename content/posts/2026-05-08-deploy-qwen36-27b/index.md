---
title: "3090 显卡部署 Qwen3.6-27B 小记"
date: 2026-05-08T14:20:08+08:00
# weight: 1
# aliases: ["/first"]
tags: ["AI", "Server", "DIY"]
author: "Square Zhong"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Qwen 的小模型是对的"
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/squarezhong.github.io/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

## 服务器环境
组里的老服务器
- 硬件配置：Intel 13900k + Nvidia 3090 24GB \* 2
- 系统：Ubuntu Server 20.04
- 软件环境：Anaconda3 和 `build-essential` 已安装
## 安装 CUDA Toolkit

查看n卡驱动以及能支持的最高 CUDA 版本
驱动版本与 CUDA 版本的兼容性可以参考[Minor Version Compatibility — CUDA Compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/minor-version-compatibility.html)
```shell
nvidia-smi
```
右上角显示该驱动支持的最高 CUDA 版本为 12.2，所有小于该版本号的 cuda-toolkit 都将被支持。这次我们安装 [CUDA Toolkit 12.1](https://developer.nvidia.com/cuda-12-1-1-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=runfile_local)
```shell
wget https://developer.download.nvidia.com/compute/cuda/12.1.1/local_installers/cuda_12.1.1_530.30.02_linux.run
sudo sh cuda_12.1.1_530.30.02_linux.run
```

如果提示以下内容：
```
│ Existing package manager installation of the driver found. It is strongly    │
│ recommended that you remove this before continuing.                          │
│ Abort                                                                        │
│ Continue                                                                     │ 
```
- 选择 Continue
- 在接下来的安装菜单界面按空格取消 Driver 栏的选择（ `[X]` 应当变为 `[ ]`）

配置环境变量
```shell
vim ~/.bashrc
```
添加以下内容：
```shell
export PATH=/usr/local/cuda-12.1/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-12.1/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

退出后重新装载配置
```shell
source ~/.bashrc
```

验证安装是否成功
```shell
nvcc --version
```
## 编译安装 llama.cpp
由于 llama.cpp 官方并没有提供预编译的 CUDA 版本的 Ubuntu 安装包，所以这里我们直接通过编译的方式安装，参考[官方文档](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md#cuda)。
若没有明确提及，下文全部操作默认在 `~/Packages` 目录进行。
```shell
git clone https://github.com/ggml-org/llama.cpp
cd llama.cpp
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release
```

由于 Ubuntu 20.4 官方仓库的最新 CMake 版本只有 3.16.3，不满足编译 llama.cpp 的最低要求（>=3.18），所以我们额外下载一个符合版本要求的 CMake。如果你的 CMake 版本满足要求，请跳过这一步。
```shell
wget https://github.com/Kitware/CMake/releases/download/v3.22.6/cmake-3.22.6-linux-x86_64.tar.gz
tar -zxvf cmake-3.22.6-linux-x86_64.tar.gz
# 写入环境变量
# 记得将路径改为你服务器上的真实路径
echo 'alias cmake-3.22="~/Packages/cmake-3.22.6-linux-x86_64/bin/cmake"' >> ~/.bashrc
source ~/.bashrc
```
使用新版 CMake 编译 llama.cpp
```shell
rm -rf build
cmake-3.22 -B build -DGGML_CUDA=ON
cmake-3.22 --build build --config Release -j
```
`-j` 参数告诉 CMake 使用所有可用的 CPU 核心去加速编译。

为方便后续使用，为`llama-cli` `llama-server`等添加 alias（可选）
```shell
echo 'alias llama-cli="~/Packages/llama.cpp/build/bin/llama-cli"' >> ~/.bashrc
echo 'alias llama-server="~/Packages/llama.cpp/build/bin/llama-server"' >> ~/.bashrc
source ~/.bashrc
```

## 部署 Qwen3.6-27B
为了避免潜在的网络方面的问题，我们选择使用 ModelScope 而不是 HuggingFace 下载模型。
前往 ModelScope 下载[千问3.6-27B](https://modelscope.cn/models/Qwen/Qwen3.6-27B)，这里我们选择 LM-Studio 提供的 [4bit 量化版本](https://modelscope.cn/models/lmstudio-community/Qwen3.6-27B-GGUF)，可以在单张 3090 24G 显卡上实现推理。
```shell
pip install modelscope
# 下载主模型权重和视觉投影器
modelscope download --model lmstudio-community/Qwen3.6-27B-GGUF Qwen3.6-27B-Q4_K_M.gguf mmproj-Qwen3.6-27B-BF16.gguf --local_dir './Packages/Qwen3.6-27B'
```

使用 llama-server 部署 Qwen3.6-27B 并挂载视觉投影器。
推荐在 tmux 中新建一个 session 运行。
```shell
tmux new -s qwen-server

# 若前面步骤没有添加 alis，请指定 llama-server 的实际路径
# --model --mmproj 请修改为你服务器上的实际路径
llama-server \
  --model ~/Packages/Qwen3.6-27B/Qwen3.6-27B-Q4_K_M.gguf \
  --mmproj ~/Packages/Qwen3.6-27B/mmproj-Qwen3.6-27B-BF16.gguf \
  --gpu-layers 99 \
  --flash-attn on \
  --ctx-size 16384 \
  --host 0.0.0.0 \
  --port 18765 \
  --api-key sk-your-custom-key
  
# 使用 ctrl+b d（先按 ctrl+b，松手，再按 d）脱离 session
# Reattach to session（抱歉不知道怎么翻译 attach）
tmux attach -t qwen-server
```
- 参数详情参考[llama.cpp/tools/server/README.md at master · ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md)
- 如果你不需要模型的视觉功能，请忽略 `--mmproj` 参数
- `--gpu-layers 99` 代表尽可能将所有模型层都塞进 VRAM
- `--ctx-size 16384` 代表上下文大小，按需修改。Qwen3.6-27B 的默认（最大）上下文大小为 262144（即256K）
- 你可以使用 `echo "sk-$(tr -dc 'A-Za-z0-9' < /dev/urandom | head -c 48)"` 来生成一个 OpenAI Style 的 api-key
- 若需要指定运行的 GPU，可以添加命令行参数 `--main-gpu 0`，0 代表第一张卡，改为 1 代表第二张卡。

你可以使用 `nvidia-smi` 监控显卡的性能开销
```shell
watch -n 1 nvidia-smi
```
也可以使用更优雅的 `nvtop`
```
sudo apt install nvtop
nvtop
```
## 调用 Qwen3.6-27B
按照上述方法部署后服务的 OpenAI Compatible Endpoint 为 `http://<your_server_ip>:18765/v1`，你可以像使用其他 API 一样使用刚刚部署 Qwen3.6-27B。
比如通过 OpenAI Python SDK 使用模型的视觉理解能力：
```shell
from openai import OpenAI
import base64

# 将图片转换为 Base64
def encode_image(image_path):
    with open(image_path, "rb") as image_file:
        return base64.b64encode(image_file.read()).decode('utf-8')

base64_image = encode_image("test.png")

client = OpenAI(
    base_url="http://<your_server_ip>:18765/v1",
    api_key="sk-our-custom-key"
)

response = client.chat.completions.create(
    model="qwen3.6-27b", # 名称随意，实际上不影响
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "Please describe the contents of this image."},
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{base64_image}"
                    }
                }
            ]
        }
    ]
)

print(response.choices[0].message.content)
```

## References
- [Minor Version Compatibility — CUDA Compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/minor-version-compatibility.html)
- https://github.com/ggml-org/llama.cpp
- https://modelscope.cn/models/Qwen/Qwen3.6-27B
