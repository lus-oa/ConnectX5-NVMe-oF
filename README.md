# ConnectX5-NVMe-oF
ConnectX5网卡配置NVMeoF测试环境
 ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨
## 1.运行环境
### 1.1 Linux内核安装
&emsp;本测试环境基于**Ubuntu 20.04,Linux 5.15.0-101-generic**.*(如果不想踩坑就跟我装一模一样的内核，实测内核版本差了一位(5.15.0-102-generic)都无法正常配置环境。)* 可使用`uname -r`查看内核版本。
#### 1.1.1 快速更改内核
&emsp;①查看已安装的内核镜像 `dpkg --get-selections | grep linux-image`  
&emsp;②安装镜像 `sudo apt-get install linux-image-5.15.0-101-generic`    
&emsp;③安装内核头文件 `sudo apt-get install linux-headers-5.15.0-101-generic`    
&emsp;④安装内核模块 `sudo apt-get install linux-modules-5.15.0-101-generic`  
&emsp;⑤安装驱动 `sudo apt-get install linux-modules-extra-5.15.0-101-generic`  
&emsp;⑥检查是否安装成功 `dpkg -l | grep 5.15.0-101-generic`   
&emsp;⑦安装完成后修改配置 `sudo vim /etc/default/grub`  
&emsp;⑧找到GRUB_DEFAULT=0，修改为 `GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu,with Linux 5.15.0-101-generic"`  
&emsp;⑨保存退出后更新 `sudo update-grub`  
&emsp;⑩重启并且 `uname -r` 检查是否安装成功

#### 1.1.2 完整安装Linux内核
&emsp;若[1.1.1](#1.1.1) 中的办法无法成功安装内核，那么需要手动编译安装内核。
具体教程点击链接[Linux内核安装教程](https://blog.csdn.net/weixin_42581825/article/details/130001118) 。

### 1.2 编译安装MLNX_OFED_LINUX驱动
&emsp;首先需要安装nvme-cli:`apt install nvme-cli`  
#### 1.2.1 下载驱动
驱动下载地址[点我](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) ,根据你自己的Ubuntu版本以及网卡版本下载对应的驱动。  
![image](https://github.com/lus-oa/ConnectX5-NVMe-oF/assets/122666739/2614c800-e049-40d3-b094-6230567c256f)  
![image](https://github.com/lus-oa/ConnectX5-NVMe-oF/assets/122666739/efe35b8a-456a-47f7-9a44-e534962ea131)  
![image](https://github.com/lus-oa/ConnectX5-NVMe-oF/assets/122666739/4a3012ee-5d9c-4fa0-b93c-1f43409d2dde)  
#### 1.2.2 编译安装
```shell
# 解压编译安装
 tar -zxvf MLNX_OFED_LINUX-24.01-0.3.3.1-ubuntu20.04-x86_64.tgz   
 cd MLNX_OFED_LINUX-24.01-0.3.3.1-ubuntu20.04-x86_64
 ./mlnxofedinstall --with-nvmf --force --add-kernel-support
# 加载相关驱动内核模块
 modprobe nvmet
 modprobe nvmet-rdma
 modprobe nvmet-tcp
 modprobe nvme-fabrics
 modprobe nvme_core
```


