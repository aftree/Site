---
title: "Dockerfile"
date: 2019-12-15T20:50:40+08:00
draft: true
# pre: "<b>1. </b>"
weight: 15
---

### FROM：指定基础镜像

第一条指令。scratch是虚拟的镜像，表示一个空白的镜像。

### RUN：执行命令



```bash
shell 格式： RUN <命令> ，RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
exec 格式： RUN ["可执行文件", "参数1", "参数2"] 。run可以写多个，每一个指令都会建立一层，所以正确写法应该是↓
RUN buildDeps='gcc libc6-dev make' \
         && apt-get update \
         && apt-get install -y $buildDeps \
         && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
         && mkdir -p /usr/src/redis \
         && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
         && make -C /usr/src/redis \
         && make -C /usr/src/redis install \
         && rm -rf /var/lib/apt/lists/* \
         && rm redis.tar.gz \
         && rm -r /usr/src/redis \
         && apt-get purge -y --auto-remove $buildDeps
```

### COPY：复制文本



```xml
COPY <源路径>... <目标路径>
COPY ["<源路径1>",... "<目标路径>"]
<源路径> 可以是多个、以及使用通配符，通配符规则满足Go的filepath.Match 规则，如：COPY hom* /mydir/    COPY hom?.txt /mydir/
<目标路径>使用 COPY 指令，源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。
```

### ADD：高级复制文件



```jsx
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
<源路径> 可以是一个 URL ，下载后的文件权限自动设置为 600 。
```

### CMD：容器启动命令



```objectivec
shell 格式： CMD <命令>
exec 格式： CMD ["可执行文件", "参数1", "参数2"...]

CMD ["nginx", "-g", "daemon off;"]
```

### ENTRYPOINT：入口点

同CMD，指定容器启动程序及参数。
 通过--entrypoint 参数在运行时替换。



```ruby
用例一：使用CMD要在运行时重新写命令才能追加运行参数，ENTRYPOINT则可以运行时接受新参数。
示例：
FROM ubuntu:16.04
RUN apt-get update \
&& apt-get install -y curl \
&& rm -rf /var/lib/apt/lists/*
ENTRYPOINT [ "curl", "-s", "http://ip.cn" ]

追加-i参数
$ docker run myip -i
......
当前 IP：61.148.226.66 来自：北京市 联通
```

### ENV：设置环境变量

在其他指令中可以直接引用ENV设置的环境变量。



```xml
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...
示例：
ENV VERSION=1.0 DEBUG=on NAME="Happy Feet"
```

### ARG：构建参数

与ENV不同的是，容器运行时不会存在这些环境变量。
 可以用  docker build --build-arg <参数名>=<值>   来覆盖。

### VOLUME：定义匿名卷



```xml
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
```

### EXPOSE：暴露端口



```xml
EXPOSE <端口1> [<端口2>...] 
EXPOSE ：EXPOSE 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。
```

### WORKDIR：指定工作目录



```bash
WORKDIR <工作目录路径>
RUN cd /app
RUN echo "hello" > world.txt
两次run不在一个环境内，可以使用WORKDIR。
```

### USER：指定当前用户



```xml
这个用户必须是事先建立好的，否则无法切换。
USER <用户名>
```

### HEALTHCHECK：健康检查



```xml
HEALTHCHECK [选项] CMD <命令> ：设置检查容器健康状况的命令
HEALTHCHECK NONE ：如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令

HEALTHCHECK 支持下列选项：
    --interval=<间隔> ：两次健康检查的间隔，默认为 30 秒；
    --timeout=<时长> ：健康检查命令运行超时时间，如果超过这个时间，本次健康检查就被视为失败，默认 30 秒；
    --retries=<次数> ：当连续失败指定次数后，则将容器状态视为 unhealthy ，默认 3次。

示例
FROM nginx
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
HEALTHCHECK --interval=5s --timeout=3s \
CMD curl -fs http://localhost/ || exit 1
```



