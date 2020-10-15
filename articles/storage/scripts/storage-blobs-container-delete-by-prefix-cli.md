---
title: Azure CLI 脚本示例 - 根据前缀删除容器 | Microsoft Docs
description: 根据容器名称前缀删除 Azure 存储 blob 容器，然后清除部署。 有关脚本示例中使用的命令，查看帮助链接。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87901872"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>根据容器名称前缀使用 Azure CLI 脚本来删除容器

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

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account) | 在指定资源组中创建 Azure 存储帐户。 |
| [az storage container create](/cli/azure/storage/container) | 在 Azure Blob 存储中创建容器。 |
| [az storage container list](/cli/azure/storage/container) | 列出 Azure 存储帐户中的容器。 |
| [az storage container delete](/cli/azure/storage/container) | 删除 Azure 存储帐户中的容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 存储的 Azure CLI 示例](../blobs/storage-samples-blobs-cli.md)中找到其他存储 CLI 脚本示例。
