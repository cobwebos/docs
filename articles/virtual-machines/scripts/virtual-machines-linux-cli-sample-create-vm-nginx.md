---
title: "Azure CLI 脚本示例 - 使用 NGINX 创建 Linux VM | Microsoft 文档"
description: "Azure CLI 脚本示例 - 使用 NGINX 创建 Linux VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f460662b17e421c0bb07c70f466a7425e5480bf9
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-vm-with-nginx"></a>使用 NGINX 创建 VM

此脚本创建一个 Azure 虚拟机，然后使用 Azure 虚拟机自定义脚本扩展安装 NGINX。 运行此脚本后，可通过虚拟机的公共 IP 地址访问演示网站。

必要时，请使用 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的说明安装 Azure CLI，然后运行 `az login` 创建与 Azure 的连接。

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "快速创建 VM")]

## <a name="custom-script-extension"></a>自定义脚本扩展

自定义脚本扩展将此脚本复制到虚拟机上。 然后运行该脚本，安装和配置 NGINX Web 服务器。 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 创建虚拟机。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | 创建网络安全组规则，以允许入站流量。 在此示例中，将为 HTTP 流量打开端口 80。 |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | 将虚拟机扩展添加到 VM 并运行该扩展。 在此示例中，使用自定义脚本扩展来安装 NGINX。|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Linux VM 文档](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。

