---
title: "Azure CLI 脚本示例 - 使用内部和外部 NSG 创建两个 VM | Microsoft 文档"
description: "Azure CLI 脚本示例 - 使用内部和外部 NSG 创建两个 VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 82d40c30c92f5da090e7ec4e2f25ead3908cc603
ms.openlocfilehash: 69a06ba68e9a92ab63fd7c86a80eb9c4718f6679
ms.lasthandoff: 03/02/2017

---

# <a name="secure-network-traffic-between-virtual-machines"></a>保护虚拟机之间的网络流量

此脚本创建两个虚拟机，并保护这两个虚拟机的传入流量。 一个虚拟机可在 Internet 上访问，其网络安全组 (NSG) 配置为允许端口 3389 和端口 80 上的流量。 第二个虚拟机无法在 Internet 上访问，其 NSG 配置为仅允许来自第一个虚拟机的流量。 

在运行此脚本前，请确保已使用 `az login` 命令创建与 Azure 的连接。 此外，应将该脚本开头的 $AdminPassword 变量更改为唯一和满足密码复杂性要求。

此示例在 Bash shell 中正常工作。 有关在 Windows 上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "使用 NSG 创建 VM")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、VM 以及所有相关资源。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | 创建 Azure 虚拟网络和子网。 |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) | 创建子网。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#update) | 更新 NSG 规则。 在此示例中，将更新后端规则以只从前端子网传递流量。 |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#list) | 返回有关网络安全组规则的信息。 在此示例中，规则名称存储在变量中，以便以后在脚本中使用。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Windows VM 文档](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。

