---
title: Azure CLI 脚本示例 - 轮换存储帐户访问密钥 | Microsoft Docs
description: 创建 Azure 存储帐户，然后检索并轮换其帐户访问密钥。
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: ac58886225221677aa003833167ff58cd578255d
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693915"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>创建存储帐户并轮换其帐户访问密钥

此脚本创建一个 Azure 存储帐户，显示新存储帐户的访问密钥，然后更新（轮换）密钥。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、存储帐户和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建存储帐户并检索和轮换其访问密钥。 表中的每一项均链接到命令特定的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account) | 在指定资源组中创建 Azure 存储帐户。 |
| [az storage account keys list](/cli/azure/storage/account/keys) | 显示指定帐户的存储帐户访问密钥。 |
| [az storage account keys renew](/cli/azure/storage/account/keys) | 重新生成主或辅助存储帐户访问密钥。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

有关其他存储 CLI 脚本示例，可参阅 [Azure Blob 存储的 Azure CLI 示例](../blobs/storage-samples-blobs-cli.md)。
