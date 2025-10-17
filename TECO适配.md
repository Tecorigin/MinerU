# MinerU

## 1. 功能概述
MinerU 是一个开源的 PDF 文档智能解析工具，旨在将复杂版式的 PDF 文件（尤其是学术论文）高保真地转换为 Markdown 格式。它集成了版面分析（如 DocLayout-YOLO）、表格识别、OCR 文字提取和视觉语言模型（VLM）等多模态 AI 技术，能够准确识别标题、段落、图表、公式和表格等内容，并保留其结构与语义。MinerU 不是一个单一模型，而是一个可扩展的处理框架，支持多种后端引擎，适用于构建高质量的文档理解与知识库构建 pipeline。

- 参考实现：
    ```
    url=https://github.com/opendatalab/MinerU
    commit_id=de41fa58590263e43b783fe224b6d07cae290a33
    ```

## 2. 快速开始
使用本工具执行推理的主要流程如下：
1. 基础环境安装：介绍推理前需要完成的基础环境检查和安装。
3. 构建Docker环境：介绍如何使用Dockerfile创建模型推理时所需的Docker环境。
4. 启动推理：介绍如何启动推理。

### 2.1 基础环境安装
请参考[Teco用户手册的安装准备章节](http://docs.tecorigin.com/release/torch_2.4/v2.2.0/#fc980a30f1125aa88bad4246ff0cedcc)，完成训练前的基础环境检查和安装。

### 2.2 构建docker
#### 2.2.1 执行以下命令，下载Docker镜像至本地（Docker镜像包：pytorch-3.0.0-torch_sdaa3.0.0.tar）

    wget 镜像下载链接(链接获取请联系太初内部人员)

#### 2.2.2 校验Docker镜像包，执行以下命令，生成MD5码是否与官方MD5码b2a7f60508c0d199a99b8b6b35da3954一致：

    md5sum pytorch-3.0.0-torch_sdaa3.0.0.tar

#### 2.2.3 执行以下命令，导入Docker镜像

    docker load < pytorch-3.0.0-torch_sdaa3.0.0.tar

#### 2.2.4 执行以下命令，构建名为MinerU的Docker容器

    docker run -itd --name="MinerU" --net=host --device=/dev/tcaicard0 --device=/dev/tcaicard1 --device=/dev/tcaicard2 --device=/dev/tcaicard3 --cap-add SYS_PTRACE --cap-add SYS_ADMIN --shm-size 64g jfrog.tecorigin.net/tecotp-docker/release/ubuntu22.04/x86_64/pytorch:3.0.0-torch_sdaa3.0.0 /bin/bash

#### 2.2.5 执行以下命令，进入名称为tecopytorch_docker的Docker容器。

    docker exec -it MinerU bash


### 2.3 执行以下命令安装MinerU 
- 安装前的准备
    ```
    cd <MinerU>
    pip install --upgrade pip
    pip install uv
    ```    
- 由于镜像中安装了torch，并且不需要安装nvidia-nccl-cu12、nvidia-cudnn-cu12等包，因此需要注释掉一部分安装依赖。
- 请注释掉<MinerU>/pyproject.toml文件中所有的"doclayout_yolo==0.0.4"依赖，并且将torch开头的包也注释掉。
- 执行以下命令安装MinerU
    ```
    uv pip install -e .[core]
    ``` 
- 下载安装doclayout_yolo==0.0.4
    ```
    pip install doclayout_yolo==0.0.4 --no-deps
    ``` 

- 下载安装其他包(doclayout_yolo==0.0.4的依赖)
    ```
    pip install albumentations py-cpuinfo seaborn thop numpy==1.24.4
    ``` 
- 由于部分张量内部内存分布不连续，需要修改如下两个文件
    <ultralytics安装路径>/ultralytics/utils/tal.py(330行左右,将view --> reshape)
    <doclayout_yolo安装路径>/doclayout_yolo/utils/tal.py(375行左右,将view --> reshape)

### 2.4 执行推理

- 开启sdaa环境
    ```
    export TORCH_SDAA_AUTOLOAD=cuda_migrate
    ```

- 首次运行推理命令前请添加以下环境下载模型权重
    ```
    export HF_ENDPOINT=https://hf-mirror.com
    ```

- 运行以下命令执行推理
    ```
     mineru   -p 'input path'  -o  'output_path' --lang 'model_name'
    ```
其中model_name可从'ch', 'ch_server', 'ch_lite', 'en', 'korean', 'japan', 'chinese_cht', 'ta', 'te', 'ka', 'latin', 'arabic', 'east_slavic', 'cyrillic', 'devanagari'选择

### 2.5 适配用到的软件栈版本列表

使用v3.0.0软件栈版本适配,获取方式联系太初内部人员
