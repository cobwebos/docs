---
title: 通过将托管磁盘附加为 OS 磁盘来创建 VM - CLI 示例
description: Azure CLI 脚本示例 - 通过将托管磁盘附加为 OS 磁盘创建 VM
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 1616466619c7c7627106c09de703d02a7c40d248
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75458402"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>通过 CLI 使用现有托管 OS 磁盘创建虚拟机

此脚本通过将现有托管磁盘附加为 OS 磁盘来创建虚拟机。 在前面的方案中使用此脚本：
* 基于从不同订阅中的托管磁盘复制的现有托管 OS 磁盘创建 VM
* 基于从专用 VHD 文件创建的现有托管磁盘创建 VM 
* 基于从快照创建的现有托管 OS 磁盘创建 VM 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令获取托管磁盘属性，将托管磁盘附加到新 VM 并创建 VM。 表中的每一项均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | 使用磁盘名称和资源组名称获取托管磁盘属性。 Id 属性用来将托管磁盘附加到新 VM |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 使用托管 OS 磁盘创建 VM |
## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
