---
title: Azure Cosmos DB API for MongoDB 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB API for MongoDB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 683da62ad78cde1a4f72b2ac0554e90d78b3ac6e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815029"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Azure Cosmos DB MongoDB API 资源

## 创建 Azure Cosmos DB API for MongoDB 帐户、数据库和集合 <a id="create-resource"></a>

使用 Azure 资源管理器模板创建 Azure Cosmos DB 资源。 此模板将创建 MongoDB API 的 Azure Cosmos 帐户，所使用的两个集合在数据库级别共享 400 RU/秒的吞吐量。 复制模板并按如下所示进行部署，或者访问 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/)，然后从 Azure 门户进行部署。 还可以将模板下载到本地计算机，或者创建新模板并使用 `--template-file` 参数指定本地路径。

> [!NOTE]
> 帐户名称必须为小写并且 < 31 个字符。

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>通过 Azure CLI 部署

若要使用 Azure CLI 部署资源管理器模板, 请**复制**该脚本, 然后选择 "**尝试**" 以打开 Azure Cloud shell。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”：

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

`az cosmosdb show` 命令显示预配后的新建 Azure Cosmos 帐户。 如果选择使用 Azure CLI 本地安装的版本, 而不是使用 CloudShell, 请参阅[Azure 命令行接口 (CLI) 一](/cli/azure/)文。

## 更新数据库的吞吐量（RU/秒）<a id="database-ru-update"></a>

以下模板将更新数据库的吞吐量。 复制模板并按如下所示进行部署，或者访问 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/)，然后从 Azure 门户进行部署。 还可以将模板下载到本地计算机，或者创建新模板并使用 `--template-file` 参数指定本地路径。

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>通过 Azure CLI 部署数据库模板

若要使用 Azure CLI 部署资源管理器模板，请选择“试用”打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”：

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## 更新集合上的吞吐量 (RU/s)<a id="collection-ru-update"></a>

以下模板将更新集合的吞吐量。 复制模板并按如下所示进行部署，或者访问 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/)，然后从 Azure 门户进行部署。 还可以将模板下载到本地计算机，或者创建新模板并使用 `--template-file` 参数指定本地路径。

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>通过 Azure CLI 部署集合模板

若要使用 Azure CLI 部署资源管理器模板，请选择“试用”打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”：

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

- [Azure 资源管理器文档](/azure/azure-resource-manager/)
- [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [排查常见的 Azure 资源管理器部署错误](../azure-resource-manager/resource-manager-common-deployment-errors.md)