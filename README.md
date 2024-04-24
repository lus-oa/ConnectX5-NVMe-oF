# ConnectX5-NVMe-oF
ConnectX5网卡配置NVMeoF测试环境
 ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨
## 1.运行环境
### 1.1 Linux内核安装
&emsp;本测试环境基于**Ubuntu 20.04,Linux 5.15.0-101-generic**.*(如果不想踩坑就跟我装一模一样的内核，实测内核版本差了一位(5.15.0-102-generic)都无法正常配置环境。)* 可使用`uname -a`查看内核版本。
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
&emsp;若[1.1.1](#1.1.1) 中的办法无法成功安装内核，那么需不要手动


