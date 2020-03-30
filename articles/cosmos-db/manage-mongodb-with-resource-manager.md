---
title: Azure Cosmos DB API for MongoDB 的资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB API for MongoDB。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063643"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Azure Cosmos DB MongoDB API 资源

本文介绍如何使用 Azure 资源管理器模板执行不同的操作来自动管理 Azure Cosmos DB 帐户、数据库和容器。 本文的示例仅适用于 Azure Cosmos DB 的 MongoDB API。若要查找适用于其他 API 类型帐户的示例，请参阅将 Azure 资源管理器模板与 Azure Cosmos DB 的 [Cassandra](manage-cassandra-with-resource-manager.md)、[Gremlin](manage-gremlin-with-resource-manager.md)、[SQL](manage-sql-with-resource-manager.md)、[表](manage-table-with-resource-manager.md) API 配合使用的相关文章。

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>为 MongoDB 帐户、数据库和集合创建 Azure Cosmos DB API<a id="create-resource"></a>

使用 Azure 资源管理器模板创建 Azure Cosmos DB 资源。 此模板将创建 MongoDB API 的 Azure Cosmos 帐户，所使用的两个集合在数据库级别共享 400 RU/秒的吞吐量。 复制模板并按如下所示进行部署，或者访问 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/)，然后从 Azure 门户进行部署。 还可以将模板下载到本地计算机，或者创建新模板并使用 `--template-file` 参数指定本地路径。

> [!NOTE]
> 帐户名称必须为小写且不超过 44 个字符。
> 若要更新 RU/秒，请重新提交包含已更新吞吐量属性值的模板。
>
> 目前，您只能使用 PowerShell 和 CLI 为 MongoDB`*.documents.azure.com`帐户创建 Azure Cosmos DB API 的 3.2 版本（即使用格式的终结点的帐户）。 要创建 3.6 版本的帐户，请使用资源管理器模板（下图）或 Azure 门户。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>通过 Azure CLI 部署

要使用 Azure CLI 部署 Azure 资源管理器模板，**请复制**脚本并选择 **"尝试它**以打开 Azure 云外壳"。 要粘贴脚本，请右键单击 shell，然后选择 **"粘贴**：

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 命令显示预配后的新建 Azure Cosmos 帐户。 如果选择使用本地安装的 Azure CLI 版本而不是使用云外壳，请参阅[Azure CLI](/cli/azure/)一文。

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

- [Azure 资源管理器文档](/azure/azure-resource-manager/)
- [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [排查常见的 Azure 资源管理器部署错误](../azure-resource-manager/templates/common-deployment-errors.md)
