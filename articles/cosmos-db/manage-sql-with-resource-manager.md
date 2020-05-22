---
title: 利用资源管理器模板创建和管理 Azure Cosmos DB
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB for Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: dfdeb210c59377822b2ee69bde286b87c2251021
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592485"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>利用 Azure 资源管理器模板管理 Azure Cosmos DB Core (SQL) API 资源

在本文中，你将了解如何使用 Azure 资源管理器模板来帮助部署和管理 Azure Cosmos DB 帐户、数据库和容器。

本文仅显示适用于 Core (SQL) API 帐户的 Azure 资源管理器模板示例。 你还可以查找 [Cassandra](manage-cassandra-with-resource-manager.md)、[Gremlin](manage-gremlin-with-resource-manager.md)、[MongoDB](manage-mongodb-with-resource-manager.md) 和[表](manage-table-with-resource-manager.md) API 的模板示例。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。

若要创建以下任何 Azure Cosmos DB 资源，请将下列示例模板复制到新的 json 文件中。 在部署具有不同名称和值的同一资源的多个实例时，可以选择创建要使用的参数 json 文件。 可以通过多种方式部署 Azure 资源管理器模板，包括 [Azure 门户](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 和 [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>具有自动缩放吞吐量的 Azure Cosmos 帐户

此模板在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为启用了大多数策略选项的自动缩放吞吐量配置的数据库和容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>具有分析存储的 Azure Cosmos 帐户

此模板在一个区域中创建一个 Azure Cosmos 帐户，其中包含启用了分析 TTL 的容器和手动或自动缩放吞吐量选项。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
       "accountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
             "description": "Cosmos DB account name"
          }
       },
       "location": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
             "description": "Location for the Cosmos DB account."
          }
       },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name for the database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name for the container"
            }
        },
        "partitionKeyPath": {
            "type": "string",
            "defaultValue": "/partitionKey",
            "metadata": {
                "description": "The partition key for the container"
            }
        },
        "throughputPolicy":{
            "type": "string",
            "defaultValue": "Autoscale",
            "allowedValues": [ "Manual", "Autoscale" ],
            "metadata": {
                "description": "The throughput policy for the container"
            }
        },
        "manualProvisionedThroughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "Throughput value when using Manual Throughput Policy for the container"
            }
        },
        "maxAutoscaleThroughput": {
            "type": "int",
            "defaultValue": 4000,
            "minValue": 4000,
            "maxValue": 1000000,
            "metadata": {
                "description": "Maximum throughput when using Autoscale Throughput Policy for the container"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]",
        "locations": 
        [ 
            {
                "locationName": "[parameters('location')]",
                "failoverPriority": 0,
                "isZoneRedundant": false
            }
        ],
        "throughputPolicy": {
            "Manual": {
                "Throughput": "[parameters('manualProvisionedThroughput')]"
            },
            "Autoscale": {
                "ProvisionedThroughputSettings": "[concat('{\"maxThroughput\":\"', parameters('maxAutoscaleThroughput'), '\"}')]"
            }            
        },
        "throughputPolicyToUse": "[if(equals(parameters('throughputPolicy'), 'Manual'), variables('throughputPolicy').Manual, variables('throughputPolicy').Autoscale)]"
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[variables('accountName')]",
            "apiVersion": "2020-03-01",
            "location": "[parameters('location')]",
            "properties": {
                "consistencyPolicy": {"defaultConsistencyLevel": "Session"},
                "databaseAccountOfferType": "Standard",
                "locations": "[variables('locations')]",
                "enableAnalyticalStorage": true
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties":{
                "resource":{
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" ],
            "properties":
            {
                "resource":{
                    "id":  "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": [ "[parameters('partitionKeyPath')]" ],
                        "kind": "Hash"
                    },
                    "analyticalStorageTtl": -1
                },
                "options": "[variables('throughputPolicyToUse')]"
            }
        }
    ]
}
```

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-manual-throughput"></a>具有标准（手动）吞吐量的 Azure Cosmos 帐户

此模板在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为启用了大多数策略选项的标准吞吐量配置的数据库和容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>具有服务器端功能的 Azure Cosmos DB 容器

此模板创建包含存储过程、触发器和用户定义函数的 Azure Cosmos 帐户、数据库和容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>免费层 Azure Cosmos DB 帐户

此模板创建一个免费层 Azure Cosmos 帐户和一个具有共享吞吐量的数据库，最多可以共享 25 个容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Azure 资源管理器文档](/azure/azure-resource-manager/)
* [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [排查常见 Azure 资源管理器部署错误](../azure-resource-manager/templates/common-deployment-errors.md)
