# CentOS设置KVM虚拟化环境


Kernel-based Virtual Machine (KVM)是一种集成到Linux的开源的虚拟化解决方案，它是一个内核模块把Linux变成物理虚拟化平台用来运行virtual machines (VMs).下面介绍在CentOS/Rocky Linux8上搭建KVM虚拟化环境。

<!--more-->

## KVM虚拟化架构图

![kvm-architecture](https://hugoblog-img-1251694304.cos.ap-guangzhou.myqcloud.com/blog/kvm-architecture.png "KVM")

## 验证环境

### 检查CPU的虚拟化技术

```bash
# Intel CPU
grep -e 'vmx' /proc/cpuinfo
# AMD CPU
grep -e 'svm' /proc/cpuinfo
```

### 验证内核的KVM模块是否开启

```bash
lsmod | grep kvm
```

## 设置cockpit web管理接口

### 安装cockpit

```bash
dnf install cockpit cockpit-machines
```

### 设置开机自启

```bash
sudo systemctl enable --now cockpit.socket
```

### 开放防火墙端口

```bash
firewall-cmd --add-service=cockpit --permanent
firewall-cmd --reload
```

使用浏览器访问`https://servr_ip:9090`端口就能打开web管理界面

## 设置虚拟化环境

### 安装虚拟化模块

```bash
dnf module install virt
dnf install virt-install virt-viewer
```

### 验证驱动

```bash
virt-host-validate
```

### 设置服务的开机自启

```bash
sudo systemctl enable --now  libvirtd.service
```

### 添加桥接设备

在cockpit web界面的网络 --> 添加桥接 --> 端口选择你的真实网卡



使用命令行添加网桥设备

```bash
# 添加一个网桥设备br0
ip link add name br0 type bridge

# 启用br0
ip link set dev br0 up

# 添加网络端口到到网桥中，要求先将端口设置为混杂模式
ip link set dev ens33 promisc on
ip link set dev ens33 up
ip link set dev ens33 master br0

# 显示现存的网桥和关联的端口
bridge link show
```

