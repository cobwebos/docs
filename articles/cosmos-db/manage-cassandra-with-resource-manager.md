---
title: Azure Cosmos DB Cassandra API 的资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB Cassandra API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: f16dec74b15f4945b54fe1423835fd8f5c8d96f1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791266"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Azure Cosmos DB Cassandra API 资源

本文介绍如何使用 Azure 资源管理器模板来帮助部署和管理 Azure Cosmos DB 帐户、大量和表。

本文仅提供 Cassandra API 帐户的示例，若要查找其他 API 类型帐户的示例，请参阅：将 Azure 资源管理器模板与[SQL](manage-sql-with-resource-manager.md)、 [Gremlin](manage-gremlin-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)、[表](manage-table-with-resource-manager.md)项目的 Azure Cosmos DB API 配合使用。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 在 Azure Cosmos 帐户中添加或删除位置时，不能同时修改其他属性。 这些操作必须单独执行。

若要创建下面的任何 Azure Cosmos DB 资源，请将下面的示例模板复制到一个新的 json 文件中。 你可以选择在部署具有不同名称和值的同一资源的多个实例时，创建要使用的参数 json 文件。 可以通过多种方式部署 Azure 资源管理器模板，包括、 [Azure 门户](../azure-resource-manager/templates/deploy-portal.md)、 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、 [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)和[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>具有自动缩放预配吞吐量的 Cassandra 的 Azure Cosmos 帐户

此模板在两个区域创建一个 Azure Cosmos 帐户，其中包含针对一致性和故障转移的选项，并为自动缩放吞吐量配置了密钥空间和表。 此模板还可用于从 Azure 快速入门模板库中单击 "部署"。

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autosscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-manual-provisioned-throughput"></a>具有标准（手动）预配吞吐量的 Cassandra 的 Azure Cosmos 帐户

此模板在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，并且为标准吞吐量配置了密钥空间和表。 此模板还可用于从 Azure 快速入门模板库中单击 "部署"。

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Azure 资源管理器文档](/azure/azure-resource-manager/)
* [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [排查常见的 Azure 资源管理器部署错误](../azure-resource-manager/templates/common-deployment-errors.md)
