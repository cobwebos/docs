---
title: "Azure CLI 脚本示例 - 使用 CLI 将托管磁盘的快照复制（移动）到同一订阅或不同订阅 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 使用 CLI 将托管磁盘的快照复制（移动）到同一订阅或不同订阅"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 7c301a314ee946bb9199650bb7f674b8dce7c141
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---

# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>使用 CLI 将托管磁盘的快照复制到同一订阅或不同订阅

此脚本会将托管磁盘的快照复制到同一订阅或不同订阅。 使用此脚本将快照移动到父快照所在区域的不同订阅。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令并使用源快照的 Id 在目标订阅中创建新快照。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | 使用快照的名称和资源组属性获取快照的所有属性。 Id 属性用于将快照复制到其他订阅。  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | 通过使用父快照的 Id 和名称在其他订阅中创建快照来复制快照。  |

## <a name="next-steps"></a>后续步骤

[基于快照创建虚拟机](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Linux VM 文档](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机和托管磁盘 CLI 脚本示例。

