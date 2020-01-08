---
title: Azure Cosmos DB Gremlin API 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB Gremlin API。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 857af7ecd9ff3d7b3c771d048a802c6d3c1e5e4f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441650"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Azure Cosmos DB Gremlin API 资源

本文介绍如何使用 Azure 资源管理器模板执行不同的操作来自动管理 Azure Cosmos DB 帐户、数据库和容器。 本文仅提供 Gremlin API 帐户的示例，若要查找其他 API 类型帐户的示例，请参阅：将 Azure 资源管理器模板与[Cassandra](manage-cassandra-with-resource-manager.md)、 [SQL](manage-sql-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)、[表](manage-table-with-resource-manager.md)项目的 Azure Cosmos DB API 配合使用。

## 创建 MongoDB 帐户、数据库和集合的 Azure Cosmos DB API<a id="create-resource"></a>

使用 Azure 资源管理器模板创建 Azure Cosmos DB 资源。 此模板将创建用于 Gremlin API 的 Azure Cosmos 帐户，该帐户具有两个在数据库级别共享 400 RU/秒吞吐量的关系图。 复制模板并按如下所示进行部署，或访问[Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/)并从 Azure 门户部署。 还可以将模板下载到本地计算机，或者创建新模板，并使用 `--template-file` 参数指定本地路径。

> [!NOTE]
> 帐户名称必须是小写、44或更少字符。
> 若要更新 RU/秒，请用更新的吞吐量属性值重新提交模板。

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-the-azure-cli"></a>部署 Azure CLI

若要使用 Azure CLI 部署 Azure 资源管理器模板，请**复制**该脚本，然后选择 "**尝试**" 以打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择 "**粘贴**"：

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

"`az cosmosdb show`" 命令显示预配新创建的 Azure Cosmos 帐户。 如果选择使用 Azure CLI 本地安装的版本，而不是使用 Cloud Shell，请参阅[Azure CLI](/cli/azure/)文章。

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

- [Azure 资源管理器文档](/azure/azure-resource-manager/)
- [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [排查常见的 Azure 资源管理器部署错误](../azure-resource-manager/resource-manager-common-deployment-errors.md)