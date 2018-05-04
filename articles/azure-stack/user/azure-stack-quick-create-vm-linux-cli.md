---
title: 在 Azure Stack 中使用 Azure CLI 创建 Linux 虚拟机 | Microsoft Docs
description: 在 Azure Stack 中使用 CLI 创建 Linux 虚拟机。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>快速入门： Azure 堆栈中使用 Azure CLI 创建 Linux 服务器虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以通过 Azure CLI 创建 Ubuntu Server 16.04 LTS 虚拟机。 按照这篇文章创建和使用虚拟机中的步骤。 这篇文章还为你提供的步骤：

* 连接到远程客户端的虚拟机。
* 安装 NGINX web 服务器并查看默认的主页。
* 清理未使用的资源。

## <a name="prerequisites"></a>必备组件

* **Azure Stack Marketplace 中的 Linux 映像**

   默认情况下，Azure Stack Marketplace 不包含 Linux 映像。 获取要提供的 Azure 堆栈运算符**Ubuntu Server 16.04 LTS**所需的映像。 运算符可以使用中所述的步骤[从 Azure 应用商店项下载到 Azure 堆栈](../azure-stack-download-azure-marketplace-item.md)文章。

* Azure 堆栈需要特定版本的 Azure CLI 来创建和管理资源。 如果你没有为 Azure 堆栈配置 Azure CLI，登录到[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)并遵循相关步骤，为[安装和配置 Azure CLI](azure-stack-version-profiles-azurecli2.md)。

* 保存你的 Windows 用户配置文件的.ssh 目录中名称 id_rsa.pub 公共 SSH 密钥。 有关创建 SSH 密钥的详细信息，请参阅[创建 SSH 密钥在 Windows 上的](../../virtual-machines/linux/ssh-from-windows.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是你可以在其中部署和管理 Azure 堆栈资源的逻辑容器。 在开发工具包或 Azure Stack 集成系统中，运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。

>[!NOTE]
 下面的代码示例中的所有变量分配值。 但是，如果你希望可以将分配新值。

以下示例在本地位置创建名为 myResourceGroup 的资源组。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

通过使用创建虚拟机[az vm 创建](/cli/azure/vm#az_vm_create)命令。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 管理用户名称和Demouser@123作为用户密码。 将这些值更改为可适合于你的环境。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

在中返回的公共 IP 地址**PublicIpAddress**参数。 记下此地址，因为你需要使用它来访问虚拟机。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

因为此虚拟机将要运行的 IIS web 服务器，你需要打开端口 80 对 Internet 流量。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令打开所需端口。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>使用 SSH 连接到虚拟机

使用安装的 SSH 客户端计算机，从连接到虚拟机。 如果你正在 Windows 客户端上，使用[Putty](http://www.putty.org/)才能创建连接。 若要连接到虚拟机，请使用以下命令：

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>安装 NGINX web 服务器

若要更新包的资源和安装最新的 NGINX 包，运行以下脚本：

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

使用安装，NGINX 和端口 80 打开虚拟机上，你可以访问 web 服务器上使用虚拟机的公共 IP 地址。 打开 Web 浏览器，并浏览到 ```http://<public IP address>```。

![NGINX web 服务器欢迎页](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

清理不不再需要的资源。 你可以使用[az 组删除](/cli/azure/group#az_group_delete)命令，以删除这些资源。 若要删除资源组及其所有资源，请运行以下命令：

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门教程，你将部署基本 Linux 服务器虚拟机与 web 服务器。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
