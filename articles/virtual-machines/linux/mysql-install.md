---
title: 在 Azure 中的 Linux VM 上设置 MySQL | Microsoft 文档
description: 了解如何在 Azure 中的 Linux 虚拟机（Ubuntu 或 Red Hat 系列操作系统）上安装 MySQL 堆栈
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158444"
---
# <a name="how-to-install-mysql-on-azure"></a>如何在 Azure 上安装 MySQL
在本文中，将了解如何在运行 Linux 的 Azure 虚拟机上安装和配置 MySQL。


> [!NOTE]
> 必须已经有一个运行 Linux 的 Microsoft Azure 虚拟机，才能完成本教程。 在继续操作前，请参阅 [Azure Linux VM 教程](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)创建并设置一个 Linux VM，其中 `mysqlnode` 为 VM 名称，`azureuser` 为用户。
> 
> 

在此情况下，请使用 3306 端口作为 MySQL 端口。  

我们将使用存储库包来安装 MySQL5.6，作为本文中的示例。 实际上，MySQL5.6 在性能上相对于 MySQL5.5 而言有更大的改进。  [此处](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)提供更多信息。

## <a name="install-mysql56-on-ubuntu"></a>在 Ubuntu 上安装 MySQL5.6
我们将使用运行 Ubuntu 的 Linux VM。


### <a name="install-mysql"></a>安装 MySQL

通过切换到安装 MySQL Server 5.6`root`用户：

```bash  
sudo su -
```

安装 mysql-server 5.6：

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
在安装过程中，会看到如下所示的对话窗口显示，要求设置 MySQL 根密码。需要在此处设置该密码。
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

再次输入密码进行确认。

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>登录
  
在 MySQL Server 安装完成后，会自动启动 MySQL 服务。 你可以登录到 MySQL 服务器使用`root`用户，并输入你的密码。

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>管理 MySQL 服务

获取 MySQL 服务的状态

```bash   
service mysql status
```
  
启动 MySQL 服务

```bash  
service mysql start
```
  
停止 MySQL 服务

```bash  
service mysql stop
```
  
重新启动 MySQL 服务

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>在 Red Hat OS、 CentOS、 Oracle Linux 上安装 MySQL
在这里，我们会将 Linux VM 用于 CentOS 或 Oracle Linux。

### <a name="add-the-mysql-yum-repository"></a>添加 MySQL yum 存储库
    
切换到`root`用户：

```bash  
sudo su -
```

下载并安装 MySQL 发行包：

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>启用 MySQL 存储库
编辑以下文件，以便允许 MySQL 存储库下载 MySQL5.6 程序包。

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
将此文件的每个值更新为下面的值：

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>安装 MySQL 

从存储库安装 MySQL。

```bash  
yum install mysql-community-server
```
  
将安装 MySQL RPM 程序包和所有相关的程序包。


## <a name="manage-the-mysql-service"></a>管理 MySQL 服务
  
检查 MySQL server 服务状态：

```bash  
service mysqld status\
```
  
检查是否正在运行的默认端口的 MySQL 服务器：

```bash  
netstat  –tunlp|grep 3306
```

启动 MySQL server:

```bash
service mysqld start
```

停止 MySQL server:

```bash
service mysqld stop
```

将 MySQL 设置启动时系统启动：

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>在 SUSE Linux 上安装 MySQL

我们在这里会通过 OpenSUSE 来使用 Linux VM。

### <a name="download-and-install-mysql-server"></a>下载并安装 MySQL Server
  
通过以下命令切换到 `root` 用户：  

```bash  
sudo su -
```
  
下载并安装 MySQL 程序包：

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>管理 MySQL 服务
  
检查 MySQL server 的状态：

```bash  
rcmysql status
```
  
检查是否 MySQL 服务器的默认端口：

```bash  
netstat  –tunlp|grep 3306
```

启动 MySQL server:

```bash
rcmysql start
```

停止 MySQL server:

```bash
rcmysql stop
```

将 MySQL 设置启动时系统启动：

```bash
insserv mysql
```

## <a name="next-step"></a>后续步骤
有关详细信息，请参阅[MySQL](https://www.mysql.com/)网站。

