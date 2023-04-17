# 如何将业务代码构建为容器镜像

### 初识容器镜像

拉取镜像

```
$ docker pull lyzhang1999/hello-world-flask:latest
```

查看本地已经拉取的镜像

```
$ docker images
```

运行镜像

```
$ docker run -d -p 8000:5000 lyzhang1999/hello-world-flask:latest
```

- `-d 代表“在后台运行容器”，同时它会输出容器 ID，这是运行容器的唯一标识。`
- `-p 代表“将容器内的 5000 端口暴露到宿主机（本地的 8000 端口）”，这可以方便我们在本地进行访问。`

查看当前运行中的容器列表

```
$ docker ps
```

进入容器内部

```
$ docker exec -it c370825640b6 bash
```

- `-it 的含义是“保持 STDIN 打开状态，并且分配一个虚拟的终端（Terminal）”。`

停止运行中的容器

```
$ docker stop c370825640b6
```

### 镜像是怎么构建出来的？

业务代码 app.py

```
from flask import Flask
import os
app = Flask(__name__)
app.run(debug=True)

@app.route('/')
def hello_world():
    return 'Hello, my first docker images! ' + os.getenv("HOSTNAME") + ''
```

Python 的依赖文件 requirements.txt

```
$ echo "Flask==2.2.2" >> requirements.txt
```

制作镜像 Dockerfile

```
# syntax=docker/dockerfile:1

FROM python:3.8-slim-buster

RUN apt-get update && apt-get install -y procps vim apache2-utils && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0"]
```

- `FROM 指定基础镜像`
- `WORKDIR 配置镜像的工作目录`
- `COPY 将本地目录或文件复制到镜像内`
- `RUN 在镜像内运行指定的命令`
- `CMD 配置镜像的启动命令`

制作镜像

```
$ docker build -t hello-world-flask .
```

- `-t 代表的是镜像名。`

查看本地镜像

```
$ docker images
```

启动镜像

```
$ docker run -d -p 8000:5000 hello-world-flask:latest
```

> 此文章为 4 月 Day17 学习笔记，内容来源于极客时间[云原生架构与 GitOps 实战](http://gk.link/a/121Vm)，强烈推荐该课程！
