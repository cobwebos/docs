---
title: 在 Azure 中的 OpenSUSE VM 上安装 MySQL | Microsoft Docs
description: 了解如何在 Azure 中的 OpenSUSE Linux 虚拟机上安装 MySQL。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: a5a6a43c41760e22a7aeb0e97aacc145c69957ff
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006390"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>在 Azure 中运行 OpenSUSE Linux 的虚拟机上安装 MySQL

[MySQL](http://www.mysql.com) 是一种常用的开源 SQL 数据库。 本教程介绍如何创建运行 OpenSUSE Linux 的虚拟机，并安装 MySQL。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>创建运行 OpenSUSE Linux 的虚拟机

首先创建一个资源组。 在此示例中，资源组名为 *mySQSUSEResourceGroup*，并在“美国东部”区域中创建。

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

创建 VM。 在此示例中，VM 名为 *myVM*，VM 大小为 *Standard_D2s_v3*，但应选择你认为最适合你的工作负荷的 [VM 大小](sizes.md)。

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

还需要向网络安全组添加一条规则以允许流量通过 MySQL 的端口 3306。

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>连接到 VM

将使用 SSH 连接到 VM。 在本示例中，VM 的公共 IP 地址为 *10.111.112.113*。 创建 VM 时，可以在输出中看到 IP 地址。

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>更新 VM
 
连接到 VM 后，安装系统更新和修补程序。 
   
```bash
sudo zypper update
```

按照提示操作以更新 VM。

## <a name="install-mysql"></a>安装 MySQL 


通过 SSH 在 VM 中安装 MySQL。 根据需要回复提示。

```bash
sudo zypper install mysql
```
 
将 MySQL 设置为在系统启动时启动。 

```bash
sudo systemctl enable mysql
```
验证是否已启用 MySQL。

```bash
systemctl is-enabled mysql
```

这应返回：已启用。

重新启动服务器。

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL 密码

在安装后，MySQL 根密码默认为空。 运行 **mysql\_secure\_installation** 脚本来保护 MySQL。 该脚本会提示更改 MySQL 根密码、删除匿名用户帐户、禁用远程根登录、删除测试数据库以及重新加载特权表。 

服务器重新启动后，重新通过 ssh 连接到 VM。

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>登录到 MySQL

现在，可以登录并输入 MySQL 提示符。

```bash  
mysql -u root -p
```
它可切换到 MySQL 提示符，你可以在其中发出用于与数据库交互的 SQL 语句。

现在，新建 MySQL 用户。

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
行尾的分号 (;) 对于结束命令很重要。


## <a name="create-a-database"></a>创建数据库


创建数据库，并授予 `mysqluser` 用户权限。

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
数据库用户名和密码仅由连接到数据库的脚本使用。  数据库用户帐户名称不一定表示系统上的实际用户帐户。

允许从另一台计算机登录。 在此示例中，允许从其登录的计算机的 IP 地址是 *10.112.113.114*。

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
若要退出 MySQL 数据库管理实用程序，请键入：

```    
quit
```


## <a name="next-steps"></a>后续步骤
有关 MySQL 的详细信息，请参阅 [MySQL 文档](http://dev.mysql.com/doc/index-topic.html)。




