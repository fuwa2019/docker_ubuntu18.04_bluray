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

`--privileged=true`

`/usr/sbin/init`

在连接nas的ssh后，输入`docker exec -t -i 容器名 /bin/bash`进入容器ssh

## 容器内部环境配置
### 更新
`apt-get -y update`

### 基本软件包安装
`apt-get install -y sudo vim wget systemctl`

### 换源 _根据自己情况选择合适的源，一般推荐aliyun_
#### 备份源列表
`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`

#### 修改sources.list文件
`sudo vim /etc/apt/sources.list`

#### 将原配置全部注释掉，再文末添加
`deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse`

#### 保存
**ESC** + `:wq!`

#### 更新源
`sudo apt-get -y update`

#### 更新软件
`sudo apt-get -y dist-upgrade`

`sudo apt-get -y upgrade`

### 手动安装部分软件包
`apt-get install -y ffmpeg imagemagick`

### 运行脚本 自动安装其他软件包
#### 说明
Aniverse/bluray提到的`bash <(wget -qO- https://git.io/bluray)`对国内环境及docker环境不友好，换成`wget -qO- https://raw.githubusercontent.com/Aniverse/bluray/master/bluray`再赋予权限后运行即可解决。
#### 具体操作
`wget -qO- https://raw.githubusercontent.com/Aniverse/bluray/master/bluray`

`chmod -R +x /usr/local/bin/bluray`

`bluray` **后续直接用此行运行脚本**

等待脚本出现提示信息后按`1`+**回车**开始自动安装，**有[y/N ]的选 y即可**

### 安装至此已完成

## 后续使用

在连接nas的ssh后，输入`docker exec -t -i 容器名 /bin/bash`进入容器ssh

输入`bluray`运行脚本

过程参照Aniverse/bluray

输出文件再nas中`你的目录/log/bluray/BDMV文件夹名称`中可见 **与Aniverse/bluray中相似**

## 参考文档

https://github.com/Aniverse/bluray#guide
https://docs.github.com/cn/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#images
https://blog.csdn.net/TracelessLe/article/details/107362505
https://blog.csdn.net/qq_46092061/article/details/118878697
https://blog.csdn.net/xiao_yi_xiao/article/details/120672705
https://blog.csdn.net/liuxiabing150/article/details/45872783
https://www.cnblogs.com/gaojia-hackerone/p/15202230.html
