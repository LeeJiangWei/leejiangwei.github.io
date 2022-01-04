---
title: Docker 部署 pytorch 应用
date: 2021-12-29 19:45:51
categories:
- 随笔
index_img: /img/about/avatar.jpg
banner_img: /img/default.jpg
---

# Docker 部署 pytorch 应用 (GPU)

## 拉取 python 3.8 镜像

```shell
docker pull python:3.8.12
```

buster, bullseye 等的区别：[In Docker image names what is the difference between Alpine, Jessie, Stretch, and Buster?](https://stackoverflow.com/questions/52083380/in-docker-image-names-what-is-the-difference-between-alpine-jessie-stretch-an)

## 构建镜像

### 编写  Dockerfile 

RUN 命令可以根据情况随意更改，这里是安装 GPU 版的 pytorch 以及其他 python 依赖。

```dockerfile
FROM python:3.8.12

WORKDIR /app

ADD . /app

RUN git reset HEAD --hard

RUN pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
RUN pip install torch==1.10.1+cu113 torchvision==0.11.2+cu113 torchaudio===0.10.1+cu113 -f https://download.pytorch.org/whl/cu113/torch_stable.html
RUN pip install -r requirements.txt

CMD python serve_pytorch.py # optional
```

### 从 Dockerfile 构建镜像

```shell
docker build -t target_name .
```

## 允许容器访问 GPU

参考：https://github.com/NVIDIA/nvidia-container-runtime

1. 安装 nvidia-container-runtime https://nvidia.github.io/nvidia-container-runtime/

2. 修改 /etc/docker/daemon.json

    ```json
    {
        "runtimes": {
            "nvidia": {
                "path": "/usr/bin/nvidia-container-runtime",
                "runtimeArgs": []
            }
        }
    }
   ```

3. 新建并运行容器
    
    ```shell
    docker run -it --gpus all image_name bash
    ```

## 常用操作

### 列出本地镜像

```shell
docker image ls
```

### 从镜像启动新的容器

```shell
docker run image_name
```

### 进入容器

```shell
docker exec -it container_name bash
```

## 参考

[Docker 从入门到实践](https://yeasy.gitbook.io/docker_practice/)

