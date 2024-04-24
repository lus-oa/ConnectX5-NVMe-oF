# ConnectX5-NVMe-oF
ConnectX5网卡配置NVMeoF测试环境
 ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨
## 1.运行环境
### 1.1 Linux内核安装
&emsp;本测试环境基于Ubuntu 20.04,Linux 5.15.0-101-generic.*(如果不想踩坑就跟我装一模一样的内核，实测内核版本差了一位(5.15.0-102-generic)都无法正常配置环境。)* 可使用`uname -a`查看内核版本。
#### 1.1.1 快速更改内核
①查看已安装的内核镜像 `dpkg --get-selections | grep linux-image`  
②安装镜像 `sudo apt-get install linux-image-5.4.0-101-generic`    
③安装内核头文件 `sudo apt-get install linux-headers-5.4.0-101-generic`    
④安装内核模块 `sudo apt-get install linux-modules-5.4.0-101-generic`  
⑤安装驱动 `sudo apt-get install linux-modules-extra-5.4.0-101-generic`  
⑥检查是否安装成功 `dpkg -l | grep 5.4.0-101-generic`   
⑦安装完成后修改配置 `sudo vim /etc/default/grub`  
⑧找到GRUB_DEFAULT=0，修改为 `GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu,with Linux 5.4.0-101-generic"`  
⑨保存退出后更新 `sudo update-grub`  
⑩重启并且 `uname -r` 检查是否安装成功




