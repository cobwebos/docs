---
title: 同一帐户中 VHD 文件中的托管磁盘 (Linux) - CLI 示例
description: Azure CLI 脚本示例 - 基于相同订阅的存储帐户中的 VHD 文件创建托管磁盘
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: bc02fb6801aa72d5c46d5fb0dc6498e5c1ded4ee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501511"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-linux"></a>使用 CLI 基于相同订阅的存储帐户中的 VHD 文件创建托管磁盘 (Linux)

此脚本基于相同订阅的存储帐户中的 VHD 文件创建托管磁盘。 使用此脚本将专用（未经过通用化/sysprep 处理）的 VHD 导入到托管 OS 磁盘以创建虚拟机。 或者使用它将数据 VHD 导入到托管数据磁盘。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令基于 VHD 创建托管磁盘。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az disk create](/cli/azure/disk) | 使用相同订阅的存储帐户中的 VHD 的 URI 创建托管磁盘 |

## <a name="next-steps"></a>后续步骤

[通过将托管磁盘附加为 OS 磁盘创建虚拟机](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机和托管磁盘 CLI 脚本示例。
