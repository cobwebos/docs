---
title: "Azure CLI 脚本示例 - 根据前缀删除容器 | Microsoft Docs"
description: "根据容器名称前缀删除 Azure 存储 blob 容器。"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 9e93dc14a4729011f74c5eafe94528608b89116f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="delete-containers-based-on-container-name-prefix"></a>根据容器名称前缀删除容器

此脚本首先会在 Azure Blob 存储中创建几个示例容器，然后根据容器名称的前缀删除一些容器。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令，删除资源组、其余容器和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令根据容器名称前缀删除容器。 表中的每一项均链接到命令特定的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 在指定资源组中创建 Azure 存储帐户。 |
| [az storage container create](/cli/azure/storage/container#az_storage_container_create) | 在 Azure Blob 存储中创建容器。 |
| [az storage container list](/cli/azure/storage/container#az_storage_container_list) | 列出 Azure 存储帐户中的容器。 |
| [az storage container delete](/cli/azure/storage/container#az_storage_container_delete) | 删除 Azure 存储帐户中的容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 存储的 Azure CLI 示例](../blobs/storage-samples-blobs-cli.md)中找到其他存储 CLI 脚本示例。
