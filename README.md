# docker_ubuntu18.04_bluray
首先感谢Aniverse大神的bluray脚本，这个方案是基于Aniverse/bluray实现的

## 功能实现：	
1、在docker环境中，利用ubuntu18.04容器实现蓝光原盘bdinfo扫描、截图、以及制作种子等一系列操作

2、用于机器一些性能较差、无法用虚拟机或像本人一样不想用虚拟机的情况

## 兼容性说明：
1、理论上来说支持安装docker的x86结构设备都支持方案，包括wsl2，可以自行尝试

2、关于linux系统的选择，推荐ubuntu18.04，其他系统或版本是否可行请自行尝试，本人测试过直至2022.8大部分操作系统及版本，综合可用性，易用性和稳定性作出的选择

3、对于映射目录，主机目录可以任选，容器目录务必统一，这是为了能完美符合脚本的命令。此外主机目录中部份文件可能因为docker环境原因或网络原因难以安装，建议在vps上运行脚本，在vps上根据容器目录下载文件，拷贝到对应被映射主机目录中进行使用。所有被映射的主机目录建议给完全访问权限

4、安装源请根据自己网络环境选择。脚本内容可见原作者github库，有需要可进行自定义，这里不会讨论
		
## 测试环境：
### 1、系统：
DSM 7.1

### 2、CPU：
N3150

### 3、
其他无关的硬件信息不赘述

## vps下载部分文件
### 1、vps上执行

`bash <(wget -qO- https://git.io/bluray)`

安装方式见Aniverse/bluray，此处不再赘述，其中要注意的是`/etc/abox/app/`下要新建文件夹`BDinfoCli.0.7`、将`bdinfocli.exe`重命名为`BDInfo.exe`、并将`BDInfo.exe`移动到`/etc/abox/app/BDinfoCli.0.7.3`，此脚本才能在vps上正常运行。_个人测试结果，请根据环境自行修改_

### 下载的文件

将文件夹目录`/etc/abox/app/`下的`BDinfoCli.0.7.3`文件夹及目录`/usr/local/`下的文件夹`bin`下载到本地并上传至nas，其中`bin`中的`smartctl`与脚本无关，请自行去留

## docker外部环境配置

`docker pull ubuntu:18.04`

`docker run \`

`--name ubuntu \`    _据情况是否修改_

`--network=bridge \`

`-v nas中bluray路径:/usr/local/bin \`    **_修改_**

`-v nas中BDinfoCli.0.7文件夹路径:/etc/abox/app \`    **_修改_**

`-v nas中BDMV路径:自行决定 \`    **_修改_**

`-v nas中log文件夹路径(包含/log):/log \`  **_修改_ log文件夹需要自行新建**

`--restart always \`  _据情况决定是否添加_

`-e HTTP_PROXY="http://你的代理IP:你的代理端口/" \`  _据情况决定是否添加_

`-d ubuntu:18.04`

在连接nas的ssh后，输入`docker exec -t -i 容器名 /bin/bash`进入容器ssh

## 容器内部环境配置
更新

`apt-get update`

换源 _根据自己情况选择合适的源，一般推荐aliyun_
