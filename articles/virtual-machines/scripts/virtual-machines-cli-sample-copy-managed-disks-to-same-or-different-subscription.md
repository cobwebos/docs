---
title: 将托管磁盘复制到订阅 - CLI 示例
description: Azure CLI 脚本示例 - 将托管磁盘复制（或移动）到同一或不同订阅
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ed593b37de556d5b62cfec6b726322d12f4d8104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051782"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>使用 CLI 将托管磁盘复制到相同或不同的订阅

此脚本会将托管磁盘复制到位于同一区域的相同或不同的订阅中。 仅当订阅属于同一 Azure AD 租户时，复制才适用。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令，通过源托管磁盘的 ID 在目标订阅中创建新的托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az disk show](/cli/azure/disk) | 使用托管磁盘的名称和资源组属性获取该托管磁盘的所有属性。 使用 ID 属性将托管磁盘复制到其他订阅。  |
| [az disk create](/cli/azure/disk) | 通过使用父托管磁盘的 ID 和名称在不同订阅中创建新的托管磁盘来复制该托管磁盘。  |

## <a name="next-steps"></a>后续步骤

[从托管磁盘创建虚拟机](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机和托管磁盘 CLI 脚本示例。
