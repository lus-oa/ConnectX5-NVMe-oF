# ConnectX5-NVMe-oF
ConnectX5网卡配置NVMeoF测试环境。  
可参考该[博客](https://blog.csdn.net/Hansyang84/article/details/121422043) 的NVMe over RDMA配置。  

## 1.运行环境
### 1.1 Linux内核安装
&emsp;本测试环境基于**Ubuntu 22.04,Linux 5.15.0-101-generic**.*(如果不想踩坑就跟我装一模一样的内核，实测内核版本差了一位(5.15.0-102-generic)都无法正常配置环境。)* 可使用`uname -r`查看内核版本。
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

### 1.3 运行调试
本测试需要用到两台主机，一台主机安装ConnectX-5网卡，并且安装网卡驱动作为client端；另一台主机安装ConnectX-5网卡和Ethernet-SSD作为target端。两主机通过网线连接，client端通过网卡去discover存储侧的存储设备，并且在client端使用FIO测试对远端的存储设备进行读写测试。用 `ibstat` 指令查看两端的网口是否为 `active` 
#### target端(存储侧）
先运行target端的指令再运行client端。  

```bash
/etc/init.d/openibd restart
systemctl restart opensmd

modprobe nvmet
modprobe nvmet-rdma
mkdir /sys/kernel/config/nvmet/subsystems/nvme-subsystem-name
cd /sys/kernel/config/nvmet/subsystems
cd nvme-subsystem-name/  # 设置允许所有主机访问
echo 1 > attr_allow_any_host
cd namespaces/  # 直接使用准备申请的NSID作为目录名创建目录
mkdir 10
cd 10
echo "/dev/nvme0n1" > device_path   # 这里关联的存储设备是/dev/sdb,根据实际情况修改
echo 1 > enable
cd /sys/kernel/config/nvmet/ports
mkdir 123
cd 123
ip addr add 101.76.209.135/24 dev ib0    # 192.168.102.25 是本机的IP地址，配置时通过ifconfig指令根据实际情况修改
echo 101.76.209.135 > addr_traddr
echo rdma > addr_trtype
echo ipv4 > addr_adrfam
echo 4420 > addr_trsvcid   

cd subsystems/
ln -s ../../../subsystems/nvme-subsystem-name nvme-subsystem-name
```

#### client端（通过网卡与远端对远端存储侧进行读写操作）
```bash
/etc/init.d/openibd restart
systemctl restart opensmd

modprobe nvmet
modprobe nvmet-rdma
modprobe nvmet-tcp
modprobe nvme-fabrics
modprobe nvme_core
ip addr add 192.168.1.160/24 dev ibp3s0f0 
nvme discover -t rdma -a 192.168.1.159 -s 4420
nvme connect -t rdma -a  192.168.1.159 -s 4420 -n  nvme-subsystem-name
```

 ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨

