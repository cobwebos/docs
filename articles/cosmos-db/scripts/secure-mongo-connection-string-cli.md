---
title: "Azure CLI 脚本 - 获取 MongoDB 应用的 Azure Cosmos DB 连接字符串| Microsoft Docs"
description: "Azure CLI 脚本示例 - 获取 MongoDB 应用的 Azure Cosmos DB 连接字符串"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sample
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 27c790db456c3e650a624b46df77d4cdce202fa7
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---

# <a name="get-an-azure-cosmos-db-connection-string-for-mongodb-apps-using-the-azure-cli"></a>使用 Azure CLI 获取 MongoDB 应用的 Azure Cosmos DB 连接字符串

此示例使用 Azure CLI 获取 MongoDB 应用的 Azure Cosmos DB 连接字符串。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[主要](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh?highlight=36-39 "获取 MongoDB 应用的 Azure Cosmos DB 连接字符串")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | 更新 Azure Cosmos DB 帐户。 |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb/list-connection-strings) | 获取帐户的连接字符串。|
| [az group delete](/cli/azure/resource#delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Cosmos DB CLI 文档](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 脚本示例。

