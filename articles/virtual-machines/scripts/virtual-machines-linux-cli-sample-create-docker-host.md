---
title: "Azure CLI 脚本示例 - 创建 Docker 主机 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 创建 Docker 主机"
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
ms.sourcegitcommit: e5362b32a0f93433c77c27a6fc116960c45404f9
ms.openlocfilehash: 3693e6f78541caeca8fd1c276028c0f64ca71ddc
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-vm-with-docker"></a>使用 Docker 创建 VM

此示例脚本创建一个虚拟机，然后使用 Azure Docker VM 扩展配置 Docker 主机。 然后，Docker VM 扩展创建一个运行 NGINX 的容器。 最后，该脚本针对端口 80 上的所有入站流量配置 Azure 网络安全组。 成功运行该脚本后，便可通过 Azure 虚拟机的 FQDN 访问 NGINX Web 服务器。 

在运行此脚本前，请确保已使用 `az login` 命令创建与 Azure 的连接。

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Docker 主机")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、VM 以及所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和网络安全组。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | 创建网络安全组规则，以允许入站流量。 在此示例中，将为 HTTP 流量打开端口 80。 |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | 将虚拟机扩展添加到 VM 并运行该扩展。 在此示例中，使用 Docker VM 扩展配置 Docker 主机。|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Linux VM 文档](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。

