---
title: "Azure CLI 脚本示例 - 从快照创建 VM | Microsoft Docs"
description: "Azure CLI 脚本示例 - 从快照创建 VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: be282f79445c505ece7c6115df7a29c20a6a5f02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>使用 CLI 从快照创建虚拟机

此脚本从 OS 磁盘的快照创建虚拟机。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建托管磁盘、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | 使用快照名称和资源组名称获取快照。 返回对象的 ID 属性用于创建托管磁盘。  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | 使用快照 ID、磁盘名称、存储类型和大小从快照创建托管磁盘  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | 使用托管 OS 磁盘创建 VM |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
