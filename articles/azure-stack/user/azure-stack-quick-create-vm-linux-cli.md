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
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 69036b522b375eced604256340b532ad14a8708e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>在 Azure Stack 中使用 Azure CLI 创建 Linux 虚拟机

*适用于：Azure Stack 集成系统*

Azure CLI 用于从命令行创建和管理 Azure Stack 资源。 本快速入门详细介绍如何在 Azure Stack 中使用 Azure CLI 创建 Linux 虚拟机。  创建 VM 之后，将安装 Web 服务器，并打开端口 80 以允许 Web 流量通过。

## <a name="prerequisites"></a>必备组件 

* 确保 Azure Stack 运营商已将“Ubuntu Server 16.04 LTS”映像添加到 Azure Stack Marketplace。 

* Azure Stack 需要使用特定版本的 Azure CLI 来创建和管理资源。 如果尚未针对 Azure Stack 配置 Azure CLI，请登录到[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或登录到基于 Windows 的外部客户端（如果[通过 VPN 建立了连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)），遵循相应的步骤[安装并配置 Azure CLI](azure-stack-version-profiles-azurecli2.md)。

* 应在 Windows 用户配置文件的 .ssh 目录中创建名为 id_rsa.pub 的 SSH 公钥。 有关创建 SSH 密钥的详细信息，请参阅[在 Windows 上创建 SSH 密钥](../../virtual-machines/linux/ssh-from-windows.md)。 

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure Stack 资源的逻辑容器。 在开发工具包或 Azure Stack 集成系统中，运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 我们已为本文档中的所有变量赋值，可以按原样使用这些值，或分配不同的值。 以下示例在本地位置创建名为 myResourceGroup 的资源组。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 作为管理用户名，使用 Demouser@123 作为密码。 更新这些值，使其适用于环境。 连接到虚拟机时需要使用这些值。

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

完成之后，该命令会输出虚拟机的参数。  请记下 *PublicIPAddress*，稍后需要使用此值来连接和管理虚拟机。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

默认情况下，仅允许通过 SSH 连接登录到 Azure 中部署的 Linux 虚拟机。 如果此 VM 将用作 Web 服务器，则需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令打开所需端口。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

在装有 SSH 的系统中，使用以下命令连接到虚拟机。 如果在 Windows 上操作，可以使用 [Putty](http://www.putty.org/) 来创建连接。 请确保替换为虚拟机的相应公共 IP 地址。 在上例中，IP 地址为 192.168.102.36。

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

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们部署了一个简单的 Linux 虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。

