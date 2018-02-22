---
title: "Azure CLI 脚本示例 - 计算 blob 容器大小 | Microsoft Docs"
description: "通过计算容器中 blob 的总大小来计算 Azure Blob 存储中容器的大小。"
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
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: f9213018969ab47ce2e78d8c119f22dedaff9452
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>计算 Blob 存储容器的大小

此脚本通过计算容器中 blob 的总大小来计算 Azure Blob 存储中容器的大小。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 此 CLI 脚本提供容器的估计大小，不应用于计费计算。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、容器和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令来计算 Blob 存储容器的大小。 表中的每一项均链接到命令特定的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage blob upload](/cli/azure/storage/account#az_storage_account_create) | 将本地文件上传到 Azure Blob 存储容器。 |
| [az storage blob list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | 列出 Azure Blob 存储容器中的 blob。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。

有关其他存储 CLI 脚本示例，可参阅 [Azure Blob 存储的 Azure CLI 示例](../blobs/storage-samples-blobs-cli.md)。
