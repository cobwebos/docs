---
title: "Azure CLI 脚本示例 - 将快照作为 VHD 导出/复制到不同区域中的存储帐户 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 将快照作为 VHD 导出/复制到相同或不同订阅中的存储帐户"
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
ms.openlocfilehash: dfb78106bc72aacee85f8412032165fdfcfc1ab3
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---

# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>使用 CLI 将托管快照作为 VHD 导出/复制到不同区域中的存储帐户

此脚本将托管快照导出到不同区域中的存储帐户。 它首先生成快照的 SAS URI，然后使用该 SAS URI 将快照复制到不同区域中的存储帐户。 可以使用此脚本在不同区域中维护托管磁盘的副本以备用于灾难恢复。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令生成托管快照的 SAS URI 并使用该 SAS URI 将快照复制到一个存储帐户。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | 生成只读 SAS，使用该 SAS 可以将基础 VHD 文件复制到存储帐户或将其下载到本地  |
| [az storage blob copy start](https://docs.microsoft.com/en-us/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | 将 blob 从一个存储帐户异步复制到另一个存储帐户 |

## <a name="next-steps"></a>后续步骤

[基于 VHD 创建托管磁盘](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[基于托管磁盘创建虚拟机](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Linux VM 文档](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机和托管磁盘 CLI 脚本示例。

