---
title: Azure CLI 脚本示例 - 创建 Windows Server VM
description: Azure CLI 脚本示例 - 创建 Windows Server VM
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: 3c0710f75df4cdd1cdf015c0acc374a3d06652b7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039875"
---
# <a name="create-a-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 创建虚拟机

该脚本将创建运行 Windows Server 2016 的 Azure 虚拟机。 运行脚本后，可通过远程桌面连接访问虚拟机。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | 创建 Azure 虚拟网络和子网。 |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | 使用静态 IP 地址和关联的 DNS 名称创建公共 IP 地址。 |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | 创建网络安全组 (NSG)，这是 Internet 和虚拟机之间的安全边界。 |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | 创建虚拟网卡并将其连接到虚拟网络、子网和 NSG。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
