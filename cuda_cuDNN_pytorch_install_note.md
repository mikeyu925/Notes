> 写在前面：
> 因为在新电脑上配置深度学习环境，因此又重新装了一下相关的环境，随手记录一下要点吧，方便以后又用。
> 主要安装了：
> 
> - cuda 10.1
> - cudnn 对应版本
> - pytorch 


## cuda安装

首先要查看是否安装了驱动
```shell
nvidia-smi
```
如果显示了一些显卡及驱动版本的信息，说明了当前有显卡驱动，否则需要安装显卡驱动。

首先说一下:`nvidia-smi`和`nvcc -V`都可以查看cuda版本，两者显示的可能不同，按照网上的说法，前者显示的是驱动最高支持的cuda版本，后者显示的是当前系统安装的cuda版本，因此只要前者版本大于后者即可。


### CUDA 下载 及 安装

[CUDA各版本地址](https://developer.nvidia.com/cuda-toolkit-archive)

选择如下版本：（按照自己希望的版本下载)
-  linux
-  x86_64
-  Ubuntu
-  18.04
-  deb

底下会给出具体的安装命令：
```shell
wget https://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda-repo-ubuntu1804-10-1-local-10.1.243-418.87.00_1.0-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804-10-1-local-10.1.243-418.87.00_1.0-1_amd64.deb
sudo apt-key add /var/cuda-repo-10-1-local-10.1.243-418.87.00/7fa2af80.pub
sudo apt-get update
sudo apt-get -y install cuda
```
建议复制wget后面的链接下载，不要用命令，因为会很慢并且容易断开。
然后依次执行后续命令。

### 配置环境变量
打开用户配置文件：
```
sudo vim ~/.bashrc
```

成功打开配置文件，在文件最后面加入如下语句：
```shell
export PATH="/usr/local/cuda-10.1/bin:$PATH"
export LD_LIBRARY_PATH="/usr/lcoal/cuda-10.1/lib64:$LD_LIBRARY_PATH"
```
保存，关闭，然后输入下面的命令使配置生效：
```shell
source ~/.bashrc
```


### 测试安装是否成功
输入如下命令，显示版本信息即表示安装成功。
```
cat /usr/local/cuda-10.1/version.txt
或者
nvcc -V
```

## 安装cuDNN


[cuDNN各版本地址](https://developer.nvidia.com/rdp/cudnn-archive)

根据安装的CUDA版本选择对应的版本，跳转至下载页面选择：
cuDNN Library for Linux 版本。

下载完后在对应目录打开终端，进行解压：
```
tar -xvf cudnn-10.1-linux-x64-v7.6.5.32.tgz
```

将解压出来的cuda目录下的include和lib64下的文件拷贝至 /usr/local/cuda-10.1下的include和lib64目录，并设置权限。

```
sudo cp cuda/include/cudnn.h /usr/local/cuda-10.1/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda-10.1/lib64
sudo chmod a+r /usr/local/cuda-10.1/include/cudnn.h /usr/local/cuda-10.1/lib64/libcudnn*
```

## 安装Pytorch

> 假设你已经安装了anaconda，并且设置了清华源镜像

### 创建虚拟环境

输入如下命令创建虚拟环境：
```
conda create -n  env_name python=3.7
```
激活环境：
```
conda activate env_name
```

这里很玄学，我用[Pytorch官网](https://pytorch.org/)给我的命令，不管怎么安装都是cpu版本的pytorch，然后最后在网上看了个别人的命令就装上GPU版本了，命令如下：
```
conda install pytorch==1.4.0 torchvision==0.5.0 cudatoolkit=10.1 -c pytorch
```
> 难道是都要指定对应文件的版本安装吗？

验证安装成功：
```python
$ python
Python 3.7.13 (default, Mar 29 2022, 02:18:16) 
[GCC 7.5.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.cuda.is_available()
True
>>> exit()
```

## Pybullet

安装pybullet比较简单，直接对于anaconda虚拟环境下执行：

```shell
conda  install -c conda-forge pybullet
```


## Pycharm

解决Pycharm下输入中文注释只能输入一个中文后续无法输入中文：
- 点击菜单 " Help >> Edit Custom VM Option..."
- 添加 -Drecreate.x11.input.method=true
- 重启Pycharm



## stable-baselines3
前往https://conda-forge.org/ 寻找 stable-baselines3 的包，根据指定命令下载
```shell
conda install -c conda-forge stable-baselines3
```