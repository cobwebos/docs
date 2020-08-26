---
title: Azure CLI 脚本示例 - 装载操作系统磁盘
description: Azure CLI 脚本示例 - 装载操作系统磁盘
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 735b16ad054edfea7cf0ad2e1e5b9c76b664e6c3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479606"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>对 VM 操作系统磁盘进行故障排除

此脚本将失败或有问题的虚拟机的操作系统磁盘作为数据磁盘装载到第二个虚拟机。 排查磁盘问题或恢复数据时，此脚本会很有用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az vm show](/cli/azure/vm) | 返回虚拟机列表。 在此示例中，查询选项用于返回虚拟机操作系统磁盘。 然后，将此值添加到名为“uri”的变量。 |
| [az vm delete](/cli/azure/vm) | 删除虚拟机。 |
| [az vm create](/cli/azure/vm) | 创建虚拟机。  |
| [az vm disk attach](/cli/azure/vm/disk) | 将磁盘附加到虚拟机。 |
| [az vm list-ip-addresses](/cli/azure/vm) | 返回虚拟机的 IP 地址。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
