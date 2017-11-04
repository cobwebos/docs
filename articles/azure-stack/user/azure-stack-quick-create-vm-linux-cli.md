---
title: "在 Azure 堆栈中使用 Azure CLI 创建 Linux 虚拟机 |Microsoft 文档"
description: "使用 Azure 堆栈中的 CLI 创建 Linux 虚拟机。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: de2ff697c083493b43ab0d1b5bcde532c28684e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>在 Azure 堆栈中使用 Azure CLI 创建 Linux 虚拟机

*适用范围： Azure 堆栈集成系统*

Azure CLI 用于创建和从命令行管理 Azure 堆栈资源。 本快速入门详细介绍如何使用 Azure CLI 在 Azure 堆栈中创建 Linux 虚拟机。  创建 VM 后，安装 web 服务器，并打开端口 80 以允许 web 流量。

## <a name="prerequisites"></a>必备组件 

* 请确保 Azure 堆栈运算符具有"Ubuntu Server 16.04 LTS"将图像添加到 Azure 堆栈市场。 

* Azure 堆栈需要特定版本的 Azure CLI 来创建和管理资源。 如果你没有为 Azure 堆栈配置的 Azure CLI，登录到[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)并遵循相关步骤，为[安装和配置 Azure CLI](azure-stack-connect-cli.md)。

* 应在 Windows 用户配置文件的.ssh 目录中创建与名称 id_rsa.pub SSH 公钥。 有关创建 SSH 密钥的详细信息，请参阅[创建 SSH 密钥在 Windows 上的](../../virtual-machines/linux/ssh-from-windows.md)。 

## <a name="create-a-resource-group"></a>创建资源组

资源组是到哪些 Azure 堆栈部署和管理资源的逻辑容器。 从开发工具包或 Azure 堆栈集成运行的系统[az 组创建](/cli/azure/group#create)命令来创建资源组。 我们已分配了本文档中的所有变量的值，你可以使用它们也将分配一个不同的值。 下面的示例创建一个名为 myResourceGroup 中的本地位置的资源组。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#create) 命令创建 VM。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 管理用户名称和Demouser@123作为密码。 更新这些值，使其适用于环境。 时连接到虚拟机，需要这些值。

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

完成后，该命令将输出参数为虚拟机。  记下*PublicIPAddress*，因为使用此连接和管理你的虚拟机。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

默认情况下，仅允许通过 SSH 连接登录到 Azure 中部署的 Linux 虚拟机。 如果此 VM 将用作 Web 服务器，则需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令打开所需端口。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

在装有 SSH 的系统中，使用以下命令连接到虚拟机。 如果在 Windows 上操作，可以使用 [Putty](http://www.putty.org/) 来创建连接。 请确保将替换为你的虚拟机的正确公共 IP 地址。 在上述示例中，IP 地址为 192.168.102.36。

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>安装 NGINX

使用以下 bash 脚本更新包源并安装最新的 NGINX 包。 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

NGINX 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 请务必使用前面记录的 *publicIpAddress* 访问默认页面。 

![NGINX 默认站点](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#delete) 命令将其删除。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在此快速入门中，你已部署简单的 Linux 虚拟机。 若要了解有关 Azure 堆栈的虚拟机的详细信息，继续到[Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)。

