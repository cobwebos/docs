---
title: Azure CLI 脚本 - 获取 MongoDB 应用的 Azure Cosmos DB 连接字符串| Microsoft Docs
description: Azure CLI 脚本示例 - 获取 MongoDB 的 Azure Cosmos DB 连接字符串
author: markjbrown
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: e2b28a370da96484c4731c9ac3867b1d86d6adab
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51009413"
---
# <a name="get-an-azure-cosmos-db-connection-string-for-mongodb-using-the-azure-cli"></a>使用 Azure CLI 获取 MongoDB 的 Azure Cosmos DB 连接字符串

此示例使用 Azure CLI 获取 MongoDB 的 Azure Cosmos DB 连接字符串。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh "Get Azure Cosmos DB connection string for MongoDB apps")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | 获取帐户的连接字符串。|
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Cosmos DB CLI 文档](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 脚本示例。
