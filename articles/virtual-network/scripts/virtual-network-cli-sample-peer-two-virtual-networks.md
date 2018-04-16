---
title: Azure CLI 脚本示例 - 对等互连两个虚拟网络 | Microsoft Docs
description: Azure CLI 脚本示例 - 对等互连两个虚拟网络。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: feab9f518076938ed20396319ceb1d5badb9eb8f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="peer-two-virtual-networks-script-sample"></a>将两个虚拟网络脚本示例对等互连

此脚本示例通过 Azure 网络在同一区域创建并连接两个虚拟网络。 运行脚本后，会在两个虚拟网络之间创建对等互连。

可以通过 Azure [Cloud Shell](https://shell.azure.com/bash) 或本地 Azure CLI 安装来执行脚本。 如果在本地使用 CLI，此脚本要求运行版本 2.0.28 或更高版本。 要查找已安装的版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果在本地运行 CLI，则还需运行 `az login` 以创建与 Azure 的连接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源：

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 下表中的每条命令均链接到特定于命令的文档：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | 创建 Azure 虚拟网络和子网。 |
| [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | 创建两个虚拟网络之间的对等互连。  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可在[虚拟网络 CLI 示例](../cli-samples.md)中查找其他虚拟网络 CLI 脚本示例。
