这个项目适用于自动驾驶的基础架构/中间件/后端开发

- 基础架构
- 中间价
- 后端开发



目前自动驾驶和车企，机器人百分之90以上都是用魔改的protobuf，有的是dds，有的是ros，这就是为什么我要做这个项目



# 学习日志





本质上这是一个高性能网络库，实现了RPC的一个高性能框架，并且采用SOA设计架构理念



### 项目简介

一个基于protobuf构建的高性能RPC框架，采用多Reactor多线程模型和回调机制，**实现SOA架构下的分布式系统通信。**

主要特点：

1. 构建环境自动化：使用docker构建整个rpc项目环境，通过dockerfile安装protobuf、cmake等依赖项，同时使用Shell编写第三方库源码安装和容器操作脚本，以实现项目构建流程的自动化和部署的便利性

2. SOA架构：项目遵循SOA的设计，即network、rpc_framework、docker分为独立的模块，提供特定的功能和接口，便于上层调用和耦合

3. network模块：基于事件驱动，非阻塞I/O和Reactor模式的网络模块

4. rpc_framework模块：基于底层network模块，proto编码、反射机制、服务注册与发现等服务，构建上层高性能RPC框架 (本质是一个C++后端网络库和webserver很相似)

5. 项目管理和构建：使用cmake作为项目的构建系统，并构建相应的静/动态库，提供第三方模块调用，并通过git,clang-format等工具，管理仓库代码

   SOA(Service-Oriented Architecture,面向服务的架构)是一种软件架构设计理念，其中软件系统被设计为由相互独立、可重用、松耦合的服务组成



grpc可以直接嵌入到上面

网络库network模块和proto_rpc模块是嵌入到项目中的，其中proto_rpc模块可以用grpc来替换？只需要保持接口一致就行？



```
docker build --network host -t network:rpc  -f network_rpc.dockerfile .
构建镜像命令分析：

```





```
make install 
[ 50%] Built target network
[ 59%] Built target rpc_proto
[ 71%] Built target rpc_framework
[ 78%] Built target test
[ 87%] Built target monitor_proto
[ 93%] Built target protobuf_rpc_client
[100%] Built target protobuf_rpc_server
Install the project...
-- Install configuration: "Debug"
-- Installing: /work/build/lib/libnetwork.a
-- Installing: /work/build/lib/librpc_proto.a
-- Installing: /work/build/lib/librpc_framework.a
-- Installing: /work/build/lib/libmonitor_proto.a
-- Installing: /work/build/bin/protobuf_rpc_server
-- Set runtime path of "/work/build/bin/protobuf_rpc_server" to ""
-- Installing: /work/build/bin/protobuf_rpc_client
-- Set runtime path of "/work/build/bin/protobuf_rpc_client" to ""
-- Installing: /work/build/bin/test

```













# proto_rpc 项目环境配置

## git 环境配置

### github ssh

```Bash
sudo apt install openssh-server
sudo systemctl status ssh
sudo ufw allow ssh

cd ~/.ssh
#若.ssh目录不存在，则，执行下面步骤
ssh-keygen -t rsa -C "xxx@xxx.com"

#拷贝 id_rsa.pub 文件内容，复制到github setting 中
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=MmZmOTAxYzk0MWZmMDQ4MDc2MGM0MzY3ZGY4ZGFjNTlfRU42c205c2liakFlWHdDa3dkSjJibWFxZDdZY1lEZ3dfVG9rZW46SU5GZmJFWnJib2VJcFB4OWlXOGNac29IbnpiXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=ZjU4M2U3MDAzMmI3MWFjZjI0ZTBlNzM2NTdhMjMzYWNfNjRrazFHdDRxQVVMR2JXWUpRSXdXWXJGUWFQNXRQeEZfVG9rZW46U1pYZmJObFo0b0FmMUF4ZVE1bGNwdTZybmJlXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

### Ubuntu git 拉取项目代码

```Bash
sudo apt install git

mkdir ~/work
cd work
git clone git@github.com:superxiaobai-1/proto_rpc.git
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=M2M4NTM0MTNjM2JlNzNkYTkwNTgzZWIyYWM0N2QyOWNfWVFmd093VWRIN1pxUk0ydzlQamYxTklNWVNxUVNUMVpfVG9rZW46UklhQWJvRHZWb04welp4OFB2R2NsWFYzbm1kXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

## vscode 远程ssh 到ubuntu

```Shell
sudo apt install net-tools
#查看ip
ifconfig 
#vscode 安装远程连接工具，进行连接
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=NGEyMWU1NzBmODBjOWJhZmU1MzkxNTIyZTBkOTcwMmFfMUVVOFBIOTMwZU5mOGIwdGVUUXhNMHlYTnVUaDBVWjNfVG9rZW46R0FyRGJkdnRRb2hldEZ4QU0yZWNVZ2o0bmxmXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

## 项目docker模块构建

### 安装docker

```Plaintext
sudo apt install curl
curl -fsSL https://test.docker.com -o test-docker.sh
sudo sh test-docker.sh
```

### 当前用户添加到docker用户组

```Plaintext
sudo sh test-docker.sh
sudo usermod -aG docker ${USER}
sudo systemctl restart docker
newgrp docker
docker ps 
```

**注：重启一下机器，让docker配置生效**

### 通过项目中dockerfile文件，构建项目镜像 

```Plain
cd ~/work/proto_rpc/docker/build
docker build --network host -t network:rpc  -f network_rpc.dockerfile .
#等待时间会比较长，镜像大约1.2G
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmNmODQzNzVkYjlhODJiNTlkZjFmNjFjMmYyZTg1MWZfcFFOeXUwMnJpV1Vka29oVVMzWmVuT3IxVnBHVkxBMTdfVG9rZW46TGd5cWJlZVBTbzZ1U0N4dGdhcGNSYjl3bmpuXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

```Plaintext
#查看镜像id,是否下载成功
docker images 

网络网络，校园网
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=MzliODRiY2E4YzU1ODI2ZTBmZGRmNmZkMzQ3ZGYxNWFfdzZPSnpINTlvN3VuQURtQzE5RDRIUDJvQnpDT0M1d1JfVG9rZW46Q0hOZGJuRklkb1UwV1R4ZkE5ZGNXekhoblpiXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

## 进入docker容器

```Plaintext
cd ~/work/proto_rpc/docker/scripts
#启动容器
./rpc_docker_run.sh
#进入容器
./rpc_docker_into.sh
```

## 编译代码

```Plaintext
#创建build目录
mkdir build
cd build
cmake ..
make -j6
make install
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=MzU5YzVlZDRiM2EzYzQxMTcyMjlmZjBlZTQ5MzE1NjZfMk5DVWVnWTEyRU5uRGFRRFpIbm5mcmJwWE5DelFXU2pfVG9rZW46TTR6M2I4ZFprb1I1WUZ4ZVhOWGNiVk5ObkxlXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

## 启动程序

```Bash
#先启动 server，进行监听
cd /work/build/bin
./protobuf_rpc_server 
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=NmI4ZTgxMDZkN2YzN2VjZDAyMDYwZDdlMWMxYjIxYmNfMWhkVlBPbjV3NWxxZmZ5R055eVdrc25UWGh2bks0M3hfVG9rZW46Q0Z3SmI4ZndtbzdHN2t4dENKaGNVekM0bkFlXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)

```Bash
#打开新终端，再次进入容器
#进入容器
cd ~/work/proto_rpc/docker/scripts
./rpc_docker_into.sh

#启动 client
cd /work/build/bin
./protobuf_rpc_client localhost
```

![img](https://r3f3348v36.feishu.cn/space/api/box/stream/download/asynccode/?code=Mjg1MDMxNmVjYzM5NjRiYTE4OTk2MjE5YmYxZmU0OTlfZmhwMFhFa3NHVW9sUnNXbVdySjRReXgxQVZPaFFGanVfVG9rZW46VnJvcGJ4UFJGb0NRZmp4bUFZNmNEZXVSbnJjXzE3MTQxMTE1MzM6MTcxNDExNTEzM19WNA)
