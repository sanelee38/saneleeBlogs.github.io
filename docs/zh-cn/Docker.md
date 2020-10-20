# Dokcer

>狂神说Docker教程笔记
>
>https://www.bilibili.com/video/BV1og4y1q7M4?from=search&seid=10974264804032601143



**镜像（image）**：

docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat镜像===>run===>tomcat01 容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

**容器（container）**：

Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的。

启动，停止，删除，基本命令！

目前就可以把这个容器理解为就是一个简易的linux系统

**仓库（repository）：**

仓库就是存放镜像的地方！

仓库分为共有仓库和私有仓库！

Docker Hub（默认是国外的）

阿里云都有容器服务器（配置镜像加速）

## 安装Docker

>环境查看
>
>```shell
>[root@VM-0-3-centos /]# uname -r
>3.10.0-1062.18.1.el7.x86_64
>```
>
>```shell
>#系统版本
>[root@VM-0-3-centos /]# cat /etc/os-release
>NAME="CentOS Linux"
>VERSION="7 (Core)"
>ID="centos"
>ID_LIKE="rhel fedora"
>VERSION_ID="7"
>PRETTY_NAME="CentOS Linux 7 (Core)"
>ANSI_COLOR="0;31"
>CPE_NAME="cpe:/o:centos:centos:7"
>HOME_URL="https://www.centos.org/"
>BUG_REPORT_URL="https://bugs.centos.org/"
>
>CENTOS_MANTISBT_PROJECT="CentOS-7"
>CENTOS_MANTISBT_PROJECT_VERSION="7"
>REDHAT_SUPPORT_PRODUCT="centos"
>REDHAT_SUPPORT_PRODUCT_VERSION="7"
>```

>安装

帮助文档：

```shell
#卸载旧的版本
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
#安装包
$ sudo yum install -y yum-utils
#设置镜像的仓库
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo#默认是国外的！
    
    yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo#阿里云镜像地址
    
#更新yum软件包索引
yum makecache fast
#安装docker docker-ce社区  ee企业
sudo yum install docker-ce docker-ce-cli containerd.io
#启动docker
systemctl start docker
#使用docker version 查看是否安装成功
[root@VM-0-3-centos /]# docker version
Client: Docker Engine - Community
 Version:           19.03.12
 API version:       1.40
 Go version:        go1.13.10
 Git commit:        48a66213fe
 Built:             Mon Jun 22 15:46:54 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.12
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.10
  Git commit:       48a66213fe
  Built:            Mon Jun 22 15:45:28 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683

```

```shell
#hello-word
docker run hello-world

#查看一下下载的 hello-word镜像
docker images

[root@VM-0-3-centos /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

```

卸载docker

```shell
#卸载依赖

$ sudo yum remove docker-ce docker-ce-cli containerd.io

#删除资源
$ sudo rm -rf /var/lib/docker

#/var/lib/docker  docker的默认工作路径！
```



#### 阿里云镜像加速

```shell
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://n053t75h.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```

## 底层原理

**Docker是怎么工作的？**

Docker是一个Client-Servcer结构的系统，Docker的守护进程运行在主机上。通过Scoket从客户端访问！

DockerServer 接收到Docker-Client的指令，就会执行这个命令！

#### Docker为什么比VM快

1. Docker有着比虚拟机更少的虚拟层
2. Docker利用的是诉诸你的内核，VM需要的是Guest OS，所以说，新建一个容器的时候，Docker不需要像虚拟机一样重新加载一个操作系统，避免引导。虚拟机是加载Guest OS，分钟级别的，而Docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级！



## Docker的常用命令

### 帮助命令

```shell
docker version  #显示Docker版本信息
docker info     #显示Docker的系统信息，包括镜像和容器的数量
docker 命令 --help #万能命令
```

### 镜像命令

Docker images 查看所有本地的主机上的镜像

```shell
[root@VM-0-3-centos /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

#解释
REPOSITORY  镜像的仓库
TAG         镜像的标签
IMAGE ID    镜像的id
CREATED     镜像的创建时间
SIZE        镜像的大小

#可选项
  -a, --all             #列出所有的镜像
 
  -q, --quiet           #只显示镜像的id

```

**docker search 命令搜索镜像**

```shell
[root@VM-0-3-centos /]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9721                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3544                [OK]        
```

**Docker pull 下载镜像**

```shell
#下载镜像  docker pull 镜像名 [:tag]
[root@VM-0-3-centos /]# docker pull mysql
Using default tag: latest  #如果不写 tag，默认就是latest
Using default tag: latest
latest: Pulling from library/mysql
8559a31e96f4: Pull complete    #分层下载，docker  image的核心   联合文件系统
d51ce1c2e575: Pull complete 
c2344adc4858: Pull complete 
fcf3ceff18fc: Pull complete 
16da0c38dc5b: Pull complete 
b905d1797e97: Pull complete 
4b50d1c6b05c: Pull complete 
c75914a65ca2: Pull complete 
1ae8042bdd09: Pull complete 
453ac13c00a3: Pull complete 
9e680cd72f08: Pull complete 
a6b5dc864b6c: Pull complete 
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6   #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest   #真实地址

#等价
docker pull mysql
docker pull docker.io/library/mysql:latest


#指定版本下载
docker pull mysql:5.7
5.7: Pulling from library/mysql
8559a31e96f4: Already exists 
d51ce1c2e575: Already exists 
c2344adc4858: Already exists 
fcf3ceff18fc: Already exists 
16da0c38dc5b: Already exists 
b905d1797e97: Already exists 
4b50d1c6b05c: Already exists 
d85174a87144: Pull complete 
a4ad33703fa8: Pull complete 
f7a5433ce20d: Pull complete 
3dcd2a278b4a: Pull complete 
Digest: sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7


```

Docker rmi  删除镜像

```shell
[root@VM-0-3-centos /]# docker rmi -f 镜像ID   #删除指定的镜像
[root@VM-0-3-centos /]# docker rmi -f 镜像ID  镜像ID 镜像ID 镜像ID  #删除多个镜像
[root@VM-0-3-centos /]# docker rmi -f $(docker images -aq)  #删除全部的镜像
```

### 容器命令

**说明：我们有了镜像才可以创建容器，linux，下载一个centos镜像来测试学习**

```shell
docker pull centos
```

**新建容器并启动**

``` shell
docker run [可选参数] image
#参数说明
--name="Name"  容器名字
-d             后台方式运行
-it            使用交互方式运行，进入容器查看内容
-p             指定容器的端口
	-p ip：主机端口：容器端口
	-p  主机端口：容器端口（常用）
	-p  容器端口
	容器端口

-P             随即制定端口

#测试 ,启动并进入容器
[root@VM-0-3-centos /]# docker run -it centos /bin/bash
[root@6bac4f0a349a /]# ls   #查看容器内部的centos  基础版本，很多命令不完善
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#从容器中退回主机
[root@6bac4f0a349a /]# exit
exit
[root@VM-0-3-centos /]# ls
bin   data  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
boot  dev   home  lib64  media       opt  root  sbin  sys  usr

```

**列出所有的运行的容器**

```shell
#docker ps 命令
-a   #列出当前正在运行的容器+带出历史运行过的容器
-n=?   #显示最近创建的容器
-q   #只显示容器的编号

[root@VM-0-3-centos /]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@VM-0-3-centos /]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                         PORTS               NAMES
6bac4f0a349a        centos              "/bin/bash"         6 minutes ago       Exited (0) 4 minutes ago                           xenodochial_herschel
1c3a85eddbed        bf756fb1ae65        "/hello"            About an hour ago   Exited (0) About an hour ago                       dreamy_ptolemy
[root@VM-0-3-centos /]# 


```

**退出容器**

```shell
exit   #直接容器停止并退出
Ctrl + P + Q #容器不停止退出
```



**删除容器**

```shell
docker rm 容器ID                 #删除指定的容器,不能删除正在运行的容器，如果要强制 rm -f
docker rm -f  $(docker ps -aq)  #删除所有的容器
docker ps -a -q|xargs docker rm #删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器ID         #启动容器
docker restart 容器ID       #重启容器
docker stop 容器ID          #停止当前正在运行的容器
docker kill 容器ID          #强制停止当前容器
```

## 常用其他命令

#### 后台启动容器

```shell
#命令 docker  run -d 镜像名
[root@VM-0-3-centos ~]# docker run -d centos

#问题 docker ps ,发现centos 停止了

#常见的坑，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

####  查看日志

```shell
docker logs -f -t --tail  容器

#自己编写一个shell脚本

[root@VM-0-3-centos ~]# docker run -d centos /bin/sh -c "while true;do echo sanelee;sleep 1;done"
3ec450a601f2ecfc398b0e06440ff4f9c0d3c28e961d371c1251c74ed6fd0a45
[root@VM-0-3-centos ~]# docker ps
CONTAINER ID        IMAGE              
3ec450a601f2        centos         

#显示日志
-tf
--tail number #要显示的日志条数

```

#### 查看容器中进程信息

```shell
#命令 docker top 容器id
[root@VM-0-3-centos ~]#  docker top 3ec450a601f2
UID                 PID                 PPID                C                   STIME               TTY                
root                4562                4546                0                   17:25               ?                       
```

#### 查看镜像的元数据

```shell
#命令
docker inspect 容器id

#测试
[root@VM-0-3-centos ~]# docker inspect 3ec450a601f2
[
    {
        "Id": "3ec450a601f2ecfc398b0e06440ff4f9c0d3c28e961d371c1251c74ed6fd0a45",
        "Created": "2020-07-11T09:25:07.481572575Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo sanelee;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 4562,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-07-11T09:25:07.942618801Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:831691599b88ad6cc2a4abbd0e89661a121aff14cfa289ad840fd3946f274f1f",
        "ResolvConfPath": "/var/lib/docker/containers/3ec450a601f2ecfc398b0e06440ff4f9c0d3c28e961d371c1251c74ed6fd0a45/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/3ec450a601f2ecfc398b0e06440ff4f9c0d3c28e961d371c1251c74ed6fd0a45/hostname",
        "HostsPath": "/var/lib/docker/containers/3ec450a601f2ecfc398b0e06440ff4f9c0d3c28e961d371c1251c74ed6fd0a45/hosts",
        "LogPath": "/var/lib/docker/containers/3ec450a601f2ecfc398b0e06440ff4f9c0d3c28e961d371c1251c74ed6fd0a45/3ec450a601f2ecfc398b0e06440ff4f9c0d3c28e961d371c1251c74ed6fd0a45-json.log",
        "Name": "/quirky_banach",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/bb80a151e43cd24be7f1b21ec0ef7dfd7fbcbbcb6a10ee1a187969cc83aa5301-init/diff:/var/lib/docker/overlay2/e3aa986e712307fe67a666607699f1c12e08b364d9b30d6b1fbe4054751b9b69/diff",
                "MergedDir": "/var/lib/docker/overlay2/bb80a151e43cd24be7f1b21ec0ef7dfd7fbcbbcb6a10ee1a187969cc83aa5301/merged",
                "UpperDir": "/var/lib/docker/overlay2/bb80a151e43cd24be7f1b21ec0ef7dfd7fbcbbcb6a10ee1a187969cc83aa5301/diff",
                "WorkDir": "/var/lib/docker/overlay2/bb80a151e43cd24be7f1b21ec0ef7dfd7fbcbbcb6a10ee1a187969cc83aa5301/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "3ec450a601f2",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo sanelee;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200611",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "d375c06e2969ec826fca61c3eaf970005962c844d8865ef2a3f6d45fb96ab202",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/d375c06e2969",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "23f19c6076b648bf89d44f60a1457d5a7a47577467ff5fcb2fa2064a956b337c",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "7264dd640e681eb6f0ffb2b95bd1f6d05ec6fb95cadc0b569b04017d87a647b8",
                    "EndpointID": "23f19c6076b648bf89d44f60a1457d5a7a47577467ff5fcb2fa2064a956b337c",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

#### 进入当前正在运行的容器

```shell
#我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

#命令
docker exec -it 容器id bashShell

#方式2
docker attach 容器id

#docker exec  #进入容器后开启一个新的终端，可以在里面操作（常用）
#docker attach #进入容器正在执行的终端，不会启动新的进程！
```

#### 从容器内拷贝文件到主机上

```shell
docker cp 容器id:容器内路径 目的地主机路径
#docker容器内新建文件
[root@90a3b6a77107 home]# touch test.java  
[root@90a3b6a77107 home]# ls
test.java
[root@90a3b6a77107 home]# exit
exit
#将文件拷贝出来到主机上
[root@VM-0-3-centos home]# docker cp 90a3b6a77107:/home/test.java /home
[root@VM-0-3-centos home]# ls
sanelee.java  test.java
[root@VM-0-3-centos home]# 

```

## 作业练习

>Docker 安装Nginx

```shell
#1、搜索镜像  search 建议去docker hub搜索，可以看到帮助文档
#2、下载镜像  pull
#3、运行测试
[root@VM-0-3-centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              0901fa9da894        29 hours ago        132MB
centos              latest              831691599b88        3 weeks ago         215MB
[root@VM-0-3-centos ~]# docker run -d --name nginx01 -p:3344:80 nginx
e9d65e26719f201017ea6e259531d823c88d47284133b7ad4e8c2461ccd76515
[root@VM-0-3-centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
e9d65e26719f        nginx               "/docker-entrypoint.…"   4 seconds ago       Up 3 seconds        0.0.0.0:3344->80/tcp   nginx01
# -d 后台运行
# --name 给容器命名
# -p 宿主机：容器内部端口

[root@VM-0-3-centos ~]# curl localhost:3344
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

```

>Docker 安装tomcat

```shell
#官方的使用
docker run -it --rm tomcat:9.0

#我们之前的启动都是后台，停止了容器之后，容器还是可以查到  docker run -it --rm，一般用来测试，用完即删

#下载再启动
docker pull tomcat

#启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

#进入容器
[root@VM-0-3-centos ~]# docker exec -it tomcat01 /bin/bash

#发现问题：1、linux命令少了；2、没有webapps。阿里云镜像的原因。默认是最小的镜像，所有不必要的都剔除。保证最小的可运行的环境
```

>部署ES+kibana

```shell
#es 暴露的端口很多
#es 十分的耗内存
#es的数据一般要放置到安全目录！挂载
# --net somenetwork 网络配置
#启动elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
```

### 可视化

- portainer（先用这个）

  ```shell
  docker run -d -p 8080:9000 \
  --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
  ```

- Rancher(CI/CD再用)

#### 什么是portainer？

Docker图形化管理工具，提供一个后台面板供我们操作！

```shell
docker run -d -p 8080:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

## Docker镜像讲解

### 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来

如何得到镜像：

- 从远程仓库下载
- 别人拷贝给你
- 自己制作一个镜像DockerFile

### Dokcer镜像加载原理

>#### UnionFS(联合文件系统)

UnionFS（联合文件系统）：Union文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层一层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终文件系统会包含所有低层的文件和目录

>#### Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

bootfs（boot file system）主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs、这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs（root file system），在bootfs之上。包含的就是典型linux系统中的/dev./proc./bin./etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了，由此可见对于不同的linux发行版本，bootfs基本是一致的，rootfs会有差别，因此不同的发行版本可以共用bootfs。

### commit镜像

```shell
docker commit  提交容器成为一个新的副本
#命令和git原理类似
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名：[TAG]
```

实战测试

```shell
# 1、启动一个默认的tomcat
# 2、发现这个磨人的tomcat是没有webapps应用，镜像的原因，官方的景象默认webapps下面没有文件的
# 3、我自己拷贝进去了基本的文件
# 4、将我们操作过的容器通过commit提交为一个镜像，以后就可以使用我修改过后的镜像即可
```

![屏幕快照 2020-07-14 上午10.04.35](/Volumes/WinToMac/笔记/屏幕快照 2020-07-14 上午10.04.35.png)

如果想要保存当前容器的转台，就可以通过commit来提交，获得一个镜像。

## 容器数据卷

### 什么是容器数据卷

**docker的理念回顾**

将应用和环境打包成一个镜像

数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！需求：数据可以持久化

MySQL，容器删了，删库跑路！需求：MySQL数据可以存储在本地！

容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到Linux上面！

**总结一句话：容器的持久化合同部操作！容器间也是可以数据共享的！**

### 使用数据卷

>方式一：直接使用命令来挂载 -v

```shell
Docker run -it -v 主机目录，容器内目录
#测试
[root@VM-0-3-centos home]# docker run -it -v /home/ceshi:/home centos /bin/bash
```

### 实战：安装MySQL

思考：MySQL的数据持久化问题！

```shell
# 获取镜像

docker pull mysql:5.7

#运行容器，需要做数据挂载

#安装启动mysql需要配置密码

#官方测试: docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my=secret-pw -d mysql:tag

#启动mysql
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器名字
[root@VM-0-3-centos ~]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
```

### 具名和匿名挂载

```shell
#匿名挂载
-v 容器内路径！
docker run -d -P --name nginx01 -v /etc/nginx nginx

#查看所有的 volume 的情况
[root@VM-0-3-centos ~]# docker volume ls
local               6f41b2ba1e8be6b95d444702c68bbc08e6e3401b7712e6843ca9233232de98b7

#这里发现，这种就是匿名挂载，我们在 -v 只写了容器内的路径，没有写容器外的路径！

#具名挂载
[root@VM-0-3-centos home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
5b36ce8f1a68c79a3a69d9ea509ae415c5deddf907aa0502c01ad22c8479411b
[root@VM-0-3-centos home]# docker volume ls
DRIVER              VOLUME NAME
local               6f41b2ba1e8be6b95d444702c68bbc08e6e3401b7712e6843ca9233232de98b7
local               e5410256003ac4142d673a0c84061e2c4c4c7ed3f8c5dd46c9a66800606d5dcb
local               juming-nginx

#通过 -v 卷名：容器内路径
#查看一下这个卷
[root@VM-0-3-centos home]# docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2020-07-14T17:03:57+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

所有docker容器内的卷，没有指定目录的情况下都是在`/var/lib/docker/volumes/xxxx`

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况在使用`具名挂载`

```shell
# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载！
-v 容器内路径 #匿名挂载
-v 卷名：容器内路径  #具名挂载
-v /宿主机路径::容器内路径  #指定路径挂载！
```

拓展

```shell
# 通过 -v 容器内路径: ro rw 改变读写权限
ro readonly #只读
rw readwrite #可读可写

#一旦设置了容器权限，容器对我们挂在出来的内容就有限定了
[root@VM-0-3-centos home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
[root@VM-0-3-centos home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

# ro  只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作！
```

### 初识Dockerfile

Dockerfile就是用来构建docker镜像的构建文件！命令脚本！

通过这个脚本可以生成镜像，镜像是一层一层的，脚本是一个一个的命令，每个命令都是一层！

```shell
#创建一个dockerfile文件，名字可以随机 建议 Dockerfile
#文件中的内容 指令（大写）  参数
FROM centos

VOLUME ["volume01","volume02"] #匿名挂载

CMD echo "-------end-------"

CMD /bin/bash
#这里的每个命令，就是镜像的一层！

[root@VM-0-3-centos docker-test-volume]# docker build -f /home/docker-test-volume/dockerfile1 -t sanelee/centos .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
 ---> 831691599b88
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in 603b7ecb44f5
Removing intermediate container 603b7ecb44f5
 ---> 5653967d961a
Step 3/4 : CMD echo "-------end-------"
 ---> Running in 8da19fa61452
Removing intermediate container 8da19fa61452
 ---> 768fc8b3c843
Step 4/4 : CMD /bin/bash
 ---> Running in a60814fdd792
Removing intermediate container a60814fdd792
 ---> 43c1d756feb8
Successfully built 43c1d756feb8
Successfully tagged sanelee/centos:latest
[root@VM-0-3-centos docker-test-volume]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
sanelee/centos        latest              43c1d756feb8        22 seconds ago      215MB
mysql                 5.7                 d05c76dbbfcf        7 hours ago         448MB
tomcat38              1.0                 7e82d2c82946        7 hours ago         652MB
nginx                 latest              0901fa9da894        3 days ago          132MB
tomcat                9.0                 6055d4d564e1        7 days ago          647MB
tomcat                latest              6055d4d564e1        7 days ago          647MB
centos                latest              831691599b88        3 weeks ago         215MB
portainer/portainer   latest              cd645f5a4769        6 weeks ago         79.1MB
elasticsearch         7.6.2               f29a1ee41030        3 months ago        791MB

```

```shell
#启动自己写的容器
[root@VM-0-3-centos docker-test-volume]# docker run -it 43c1d756feb8 /bin/bash
[root@9a5c1c329208 /]# ls -l
total 56
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  360 Jul 14 09:31 dev
drwxr-xr-x   1 root root 4096 Jul 14 09:31 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Jun 11 02:35 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 105 root root    0 Jul 14 09:31 proc
dr-xr-x---   2 root root 4096 Jun 11 02:35 root
drwxr-xr-x  11 root root 4096 Jun 11 02:35 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 Jul 14 09:31 sys
drwxrwxrwt   7 root root 4096 Jun 11 02:35 tmp
drwxr-xr-x  12 root root 4096 Jun 11 02:35 usr
drwxr-xr-x  20 root root 4096 Jun 11 02:35 var
drwxr-xr-x   2 root root 4096 Jul 14 09:31 volume01  #这两个目录就是我们生成镜像时自动挂载的数据卷目录
drwxr-xr-x   2 root root 4096 Jul 14 09:31 volume02  #

```

这个卷和外部一定有一个同步的目录

查看一下卷挂载的路径

```shell
[root@VM-0-3-centos ~]# docker inspect 839d5dd5c487
。。。。。。
"Mounts": [
            {
                "Type": "volume",
                "Name": "c2b628d81da8fdfb9fcdbaf01a343ab87aa959f469ef04fa4f8483697855ae00",
                "Source": "/var/lib/docker/volumes/c2b628d81da8fdfb9fcdbaf01a343ab87aa959f469ef04fa4f8483697855ae00/_data", # 挂载的外部路径
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "9f5e00baa75156f7099185608271da652fe863416aedf0385826e596aa8deb3f",
                "Source": "/var/lib/docker/volumes/9f5e00baa75156f7099185608271da652fe863416aedf0385826e596aa8deb3f/_data", # 挂载的外部路径
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],

。。。。。。
```

### 数据卷容器

多个mysql同步数据

```shell
#启动3个容器，通过我们刚才自己写的镜像启动
[root@VM-0-3-centos /]# docker run -it --name docker01 43c1d756feb
[root@525097f7d9ec /]# ls -l
total 56
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  360 Jul 14 09:48 dev
drwxr-xr-x   1 root root 4096 Jul 14 09:48 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Jun 11 02:35 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 115 root root    0 Jul 14 09:48 proc
dr-xr-x---   2 root root 4096 Jun 11 02:35 root
drwxr-xr-x  11 root root 4096 Jun 11 02:35 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 Jul 14 09:31 sys
drwxrwxrwt   7 root root 4096 Jun 11 02:35 tmp
drwxr-xr-x  12 root root 4096 Jun 11 02:35 usr
drwxr-xr-x  20 root root 4096 Jun 11 02:35 var
drwxr-xr-x   2 root root 4096 Jul 14 09:48 volume01
drwxr-xr-x   2 root root 4096 Jul 14 09:48 volume02


#docker02 挂载了01    
--volumes-from  
[root@VM-0-3-centos /]# docker run -it --name docker02 --volumes-from docker01 sanelee/centos
```

多个mysql实现数据共享

```shell
[root@VM-0-3-centos /]# docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01 mysql:5.7

#这个时候可以实现两个容器数据同步
```

结论：容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

但是一旦持久化到了本地，这个时候本地的数据是不会删除的

## DockerFile

dockerfile是用来构建docker镜像的文件1命令参数脚本！

构建步骤：

1. 编写一个dockerfile文件
2. docker build构建成为一个镜像
3. docker run运行镜像
4. docker push发布镜像（DockerHub、阿里云镜像仓库！）

很多官方镜像都是基础包，很多功能都没有，我们通常会自己搭建自己的镜像

### DockerFile构建过程

**基础知识**：

1. 每个保留关键字（指令）都是必须是大写字母
2. 执行从上到下顺序执行
3. #表示注释
4. 每一个指令都会差UN构建提交一个新的镜像层，并提交



![img](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1982640279,3003728739&fm=11&gp=0.jpg)

Dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！

步骤：开发，部署，运维。。。缺一不可

Dockerfile：构建文件，定义了一切的步骤，源代码

DockerImage：通过DockerFile构建生成的镜像，最终发布和运行的产品

Docker容器：容器就是镜像运行起来提供服务的



### DockerFiled指令

```shell
FROM          #基础镜像，一切从这里开始构建
MAINTAINER    #镜像是谁写的，姓名+邮箱
RUN           #镜像构建的时候需要运行的命令
ADD						#步骤：tomcat镜像，这个tomcat压缩包，添加内容
WORKDIR				#镜像的工作目录
VOLUME 				#挂载的目录
EXPOSE				#暴露端口配置
CMD 					#指定这个容器启动的时候运行的命令，这个只有最后一个会生效，可被替代
ENTRYPOINT    #指定这个容器启动的时候运行的命令，可以追加命令
ONBUILD				#当构建一个被继承 DockerFile  这个时候就会运行ONBUILD的指令。触发指令
COPY					#类似ADD ，将我们的文件拷贝到镜像中
ENV						#构建的时候设置环境变量
```



![img](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=268974649,2607019911&fm=26&gp=0.jpg)

### 实战测试

Docker Hub中99%镜像都是从这个基础镜像过来的 FROM scratch，然后配置需要的软件和配置来进行构建的

<img src="/Volumes/WinToMac/笔记/image-20200714223247149.png" alt="image-20200714223247149" style="zoom:150%;" />

>创建一个自己的centos

```shell
# 1、编写Dockerfile的文件
FROM centos

MAINTAINER sanelee<18710731037@163.com>

ENV MYPATH /usr/local

WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH

CMD echo "------end------"
CMD /bin/bash 

# 2、通过这个文件构建镜像
# 命令 docker build -f dockerfile文件路径  -t 镜像名:[tag]
[root@VM-0-3-centos dockerfile]# docker build -f mydockerfile-centos -t mycentos .
Successfully built 67b0d59d2592
Successfully tagged mycentos:latest

```

>CMD和ENTRYPOINT的区别

```shell
CMD 					#指定这个容器启动的时候运行的命令，这个只有最后一个会生效，可被替代
ENTRYPOINT    #指定这个容器启动的时候运行的命令，可以追加命令
```

测试cmd

```shell
#编写 dockerfile 文件
[root@VM-0-3-centos dockerfile]# vim dockerfile-cmd-test
FROM centos
CMD ["ls","-a"]

#构建镜像
[root@VM-0-3-centos dockerfile]# docker build -f dockerfile-cmd-test -t cmdtest .
 
 #run运行。发现命令生效
 [root@VM-0-3-centos dockerfile]# docker run 4578afbf2b38
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
#想追加一个命令 -l ls -al
[root@VM-0-3-centos dockerfile]# docker run 4578afbf2b38 -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\": executable file not found in $PATH": unknown.

#cmd的情况下 -l 替换了CMD ["ls","-a"] 命令，-l不是命令所以报错
```

测试 ENTRYPONIT

```shell
[root@VM-0-3-centos dockerfile]# vim dockerfile-cmd-entrypoint
FROM centos
ENTRYPOINT ["ls","-a"]

[root@VM-0-3-centos dockerfile]# docker build -f dockerfile-cmd-entrypoint -t entrypoint-test .

[root@VM-0-3-centos dockerfile]# docker run 5343328bab68
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

[root@VM-0-3-centos dockerfile]# docker run 5343328bab68 -l
total 56
drwxr-xr-x   1 root root 4096 Jul 15 01:04 .
drwxr-xr-x   1 root root 4096 Jul 15 01:04 ..
-rwxr-xr-x   1 root root    0 Jul 15 01:04 .dockerenv
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  340 Jul 15 01:04 dev
drwxr-xr-x   1 root root 4096 Jul 15 01:04 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Jun 11 02:35 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 102 root root    0 Jul 15 01:04 proc
dr-xr-x---   2 root root 4096 Jun 11 02:35 root
drwxr-xr-x  11 root root 4096 Jun 11 02:35 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 Jul 14 09:31 sys
drwxrwxrwt   7 root root 4096 Jun 11 02:35 tmp
drwxr-xr-x  12 root root 4096 Jun 11 02:35 usr
drwxr-xr-x  20 root root 4096 Jun 11 02:35 var

```

Dockerfile中很多命令都十分相似，我们需要了解它们的区别，我们最好的学习就是对比他们然后测试效果！

### 实战：Tomcat镜像

1. 准备镜像文件 tomcat压缩包，jdk的压缩包

   ```shell
   [root@VM-0-3-centos tomcat]# ll
   总用量 151252
   -rw-r--r-- 1 root root  11765936 7月  15 09:52 apache-tomcat-9.0.37.zip
   -rw-r--r-- 1 root root 143111803 7月  15 09:50 jdk-8u261-linux-x64.tar.gz
   
   ```

2. 编写dockerfile文件，官方命名`Dockerfile`,build会自动寻找这个文件，就不需要 -f 指定了！

   ```shell
   FROM centos
   
   MAINTAINER sanelee<18710731037@163.com>
   
   COPY readme.txt /usr/local/readme.txt
   
   ADD jdk-8u261-linux-x64.tar.gz /usr/local/
   ADD apache-tomcat-9.0.37.tar.gz /usr/local/
   
   RUN yum -y install vim
   
   ENV MYPATH /usr/local
   
   WORKDIR $MYPATH
   
   ENV JAVA_HOME /usr/local/jdk1.8
   ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
   ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.37
   ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.37
   ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
   
   EXPOSE 8080
   
   CMD /usr/local/apache-tomcat-9.0.37/bin/startup.sh && tail -F /url/local/apache-tomcat-9.0.37/bin/logs/catalina.out
   ```

3. 构建镜像

   ```shell
   #docker build -t diytomcat .
   
   ```

4. 启动镜像

5. 访问测试

6. 发布醒目（由于做了卷挂载，我们直接在本地编写项目就可以发布了）

   ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <web-app xmlns="http://java.sun.com/xml/ns/javaee"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                                  http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
              version="2.5">
   
     </web-app>
   ```

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8"
       pageEncoding="UTF-8"%>
   <!DOCTYPE html>
   <html>
   <head>
   <meta charset="utf-8">
   <title>hello.sanelee</title>
   </head>
   <body>
   Hello World!<br/>
   <%
   System.out.println("----my test web logs-----");
   %>
   </body>
   </html>
   ```

   





## Docker 网络

### 理解Docker0



![image-20200720104339924](/Volumes/WinToMac/笔记/image-20200720104339924.png)

>1.lo  本机回环地址
>
>2.eth0  阿里云内网地址
>
>3.docker0  docker0 地址

三个网络

```shell
#问题  docker 是如何处理容器网络访问的

[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker run -d -P --name tomcat01 tomcat
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
8: eth0@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever


#思考  linux 能不能pin通容器内部
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.112 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.079 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.069 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.080 ms

#linux可以ping通docker容器内部

```

>原理

1. 我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0桥接模式，使用的技术是 evth-pair技术！

   再次测试ip addr

![屏幕快照 2020-07-20 上午10.58.25](/Volumes/WinToMac/笔记/屏幕快照 2020-07-20 上午10.58.25.png)

```shell
#我们发现这个容器的网卡，都是一对一对的
# evth-pair 就是一对虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连
#正因为有这个特性，evth-pair 充当一个桥梁
```

容器之间可以互相ping通的

两个容器之间是公用的一个路由器，docker0

所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用IP

Docker中的所有的网络接口都是虚拟的，虚拟的转发效率高！（内网传递文件！）

只要容器删除，对应网桥一对就没了

### ---link

>思考一个场景，我们编写了一个微服务，database url = ip：，项目不重启，数据库id换掉了，我们希望可以处理这个问题，可以用名字来进行访问容器

```shell
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

#如何解决
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
16ac494f7c15396db53bd2f131117715f4d40abd9d7cf7a230fca473b109dd30
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.150 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.108 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=3 ttl=64 time=0.096 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=4 ttl=64 time=0.074 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=5 ttl=64 time=0.111 ms

#反向可以ping通吗？
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known
#不可以！
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known

```

本质探究：--link就是我们在hosts配置中增加了一个172.18.0.3 tomcat02 16ac494f7c15396

我们现在玩Docker已经不建议使用 --link了！

自定义网络！不适用docker0

docker0问题：它不支持容器名连接访问



### 自定义网络

>查看所有的docker网络

```shell
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
97392c300e2f        bridge              bridge              local
3b10e5deb512        host                host                local
c46f393c0a07        none                null                local
```

### 网络模式

bridge：桥接  docker（默认，自己创建也使用bridge模式）

none：不配置网络

host：和宿主机共享网络

container：容器网络连通（局限性大，用得少）

#### 测试

```shell
#我们直接启动的命令 --net bridge ，而这个就是我们的docker0
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker run -d -P --name tomcat01 --net bridge tomcat

#docker0特点：默认，域名不能访问，--link可以打通连接

#我们可以自定义一个网络
# --driver bridge
# --subnet 192.168.0.0/16
# --gateway 192.168.0.1
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
b6a12f4cac01fe48a49c4381bd5f51867a9043e6abb5fdae1b2ee38f215bf731

[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
97392c300e2f        bridge              bridge              local
3b10e5deb512        host                host                local
b6a12f4cac01        mynet               bridge              local
c46f393c0a07        none                null                local

```

>我们自己创建的网络
>
>```shell
>[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker network inspect mynet
>[
>    {
>        "Name": "mynet",
>        "Id": "b6a12f4cac01fe48a49c4381bd5f51867a9043e6abb5fdae1b2ee38f215bf731",
>        "Created": "2020-07-20T15:31:49.897904341+08:00",
>        "Scope": "local",
>        "Driver": "bridge",
>        "EnableIPv6": false,
>        "IPAM": {
>            "Driver": "default",
>            "Options": {},
>            "Config": [
>                {
>                    "Subnet": "192.168.0.0/16",
>                    "Gateway": "192.168.0.1"
>                }
>            ]
>        },
>        "Internal": false,
>        "Attachable": false,
>        "Ingress": false,
>        "ConfigFrom": {
>            "Network": ""
>        },
>        "ConfigOnly": false,
>        "Containers": {},
>        "Options": {},
>        "Labels": {}
>    }
>]
>```
>
>再次测试ping连接
>
>```shell
>[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec tomcat-net-01 ping 192
>connect: Invalid argument
>[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec tomcat-net-01 ping 192.168.0.3
>PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
>64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.118 ms
>64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.096 ms
>64 bytes from 192.168.0.3: icmp_seq=3 ttl=64 time=0.081 ms
>#现在不使用--link也可以ping名字了
>[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec tomcat-net-01 ping tomcat-net-02
>PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
>64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.069 ms
>64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.083 ms
>64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.092 ms
>
>```

我们自定义的网络docker都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络



好处：

​	redis~不同的集群使用不同的网络，保证集群是安全和健康的

​	mysql-不同的集群使用不同的网络，保证集群是安全和健康的

### 网络连通

```shell
#测试打通

#连通之后就是将tomcat01 放到了mynet 网络下

#一个容器两个ip
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker network connect mynet tomcat01
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker network inspect mynet 
[
    {
        "Name": "mynet",
        "Id": "b6a12f4cac01fe48a49c4381bd5f51867a9043e6abb5fdae1b2ee38f215bf731",
        "Created": "2020-07-20T15:31:49.897904341+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "04b25e79f73600a6d2483296f19b86929eb2c51d3e5bc236fa95a51d591a940a": {
                "Name": "tomcat-net-02",
                "EndpointID": "98726ce8e03e9b983b53c75e5187d2692c7cd6a2bd8b77fbb4f2af55b1211923",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "bf269588386a130f2b04b50efb0fdd947ee9638581359cd6d691e5887fa04c93": {
                "Name": "tomcat01",
                "EndpointID": "13564c0c6736da9f3d6786fb59ce6ea637543f659ba5f78b70cf5dea1f14c8b9",
                "MacAddress": "02:42:c0:a8:00:04",
                "IPv4Address": "192.168.0.4/16",
                "IPv6Address": ""
            },
            "d1aca704befe7aa32137646cc3186d97c6cc425c6a2438a7ee7c9a353da0a766": {
                "Name": "tomcat-net-01",
                "EndpointID": "d4aab6c330d3462ee6b56e2cbf38b2c2eb907ef3a7fe1e4fedcb10f9ffab8909",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
#01 连通
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec -it tomcat01 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.114 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.083 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.086 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=4 ttl=64 time=0.086 ms

#02 是依旧打不通的
[root@iZ8vb9f7hiv5r7rhlu46f3Z ~]# docker exec -it tomcat02 ping tomcat-net-01
ping: tomcat-net-01: Name or service not known
```

结论：假设要跨网络操作别人，就需要docker network connect连通！



### 实战：部署Redis集群

shell脚本！

```shell
#创建网卡
docker network create redis --subnet 172.38.0.0、16

#通过脚本创建六个redis配置
for port in $(seq 1 6);\
do \mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/www/server/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:6.0.5 redis-server /www/server/redis/redis.conf

docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /mydata/redis/node-1/data:/data \
-v /mydata/redis/node-1/conf/redis.conf:/www/server/redis/redis.conf \
-d --net redis --ip 172.38.0.11 redis:6.0.5 redis-server /www/server/redis/redis.conf

docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
-v /mydata/redis/node-2/data:/data \
-v /mydata/redis/node-2/conf/redis.conf:/www/server/redis/redis.conf \
-d --net redis --ip 172.38.0.12 redis:6.0.5 redis-server /www/server/redis/redis.conf

docker run -p 6373:6379 -p 16373:16379 --name redis-3 \
-v /mydata/redis/node-3/data:/data \
-v /mydata/redis/node-3/conf/redis.conf:/www/server/redis/redis.conf \
-d --net redis --ip 172.38.0.13 redis:6.0.5 redis-server /www/server/redis/redis.conf

docker run -p 6374:6379 -p 16374:16379 --name redis-4 \
-v /mydata/redis/node-4/data:/data \
-v /mydata/redis/node-4/conf/redis.conf:/www/server/redis/redis.conf \
-d --net redis --ip 172.38.0.14 redis:6.0.5 redis-server /www/server/redis/redis.conf

docker run -p 6375:6379 -p 16375:16379 --name redis-5 \
-v /mydata/redis/node-5/data:/data \
-v /mydata/redis/node-5/conf/redis.conf:/www/server/redis/redis.conf \
-d --net redis --ip 172.38.0.15 redis:6.0.5 redis-server /www/server/redis/redis.conf

docker run -p 6376:6379 -p 16376:16379 --name redis-6 \
-v /mydata/redis/node-6/data:/data \
-v /mydata/redis/node-6/conf/redis.conf:/www/server/redis/redis.conf \
-d --net redis --ip 172.38.0.16 redis:6.0.5 redis-server /www/server/redis/redis.conf
#创建集群
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
```

docker搭建redis集群完成！

```shell
127.0.0.1:6379> cluster nodes
0bb842f07a2751a08e8c359c2c0e14385853cabd 172.38.0.15:6379@16379 slave a833610470e608bbcc9a37ee3ea28aa959053904 0 1595294663247 5 connected
2f6771ce7491a8b9f1776ddd230b5b9917f2d776 172.38.0.12:6379@16379 master - 0 1595294665272 2 connected 5461-10922
a580d2a154c86bf462f6828d4668472b7a064b31 172.38.0.14:6379@16379 slave aa5d444e0e41e086fea96859decceb2178b30ccc 0 1595294664771 4 connected
04c7355cf5a3354a3fa41564f6d1c250d3be9f02 172.38.0.16:6379@16379 slave 2f6771ce7491a8b9f1776ddd230b5b9917f2d776 0 1595294664248 6 connected
aa5d444e0e41e086fea96859decceb2178b30ccc 172.38.0.13:6379@16379 master - 0 1595294663000 3 connected 10923-16383
a833610470e608bbcc9a37ee3ea28aa959053904 172.38.0.11:6379@16379 myself,master - 0 1595294665000 1 connected 0-5460
```

使用了docker之后，所有的技术都会慢慢的变得简单起来！



### SpringBoot微服务打包Docker镜像

1. 构架SpringBoot项目
2. 打包应用
3. 编写dockerfile
4. 构建镜像
5. 发布运行！