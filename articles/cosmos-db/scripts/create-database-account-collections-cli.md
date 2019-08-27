---
title: Azure CLI 脚本 - 创建 Azure Cosmos DB SQL API 帐户、数据库和容器
description: Azure CLI 脚本示例 - 创建 Azure Cosmos DB SQL API 帐户、数据库和容器
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 0ff6d0ea2701eab8f7cc98f2aeead7492b231f77
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616747"
---
# <a name="azure-cosmos-db-create-a-sql-api-account-using-cli"></a>Azure Cosmos DB：使用 CLI 创建 SQL API 帐户

此示例 CLI 脚本创建一个 Azure Cosmos DB SQL API 帐户、数据库和容器。  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-account-database/create-cosmosdb-account-database.sh "Create an Azure Cosmos DB SQL API account, database, and container.")]

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | 创建 Azure Cosmos 数据库。 |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | 创建 Azure Cosmos 容器。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Cosmos DB CLI 文档](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 脚本示例。
