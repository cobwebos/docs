---
title: 快速入门 - 使用 Azure 资源管理器模板创建 Azure Cosmos DB 和容器
description: 快速入门 - 展示了如何使用 Azure 资源管理器模板创建 Azure Cosmos 数据库和容器
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: e626f6e5b65e369c3c77900cd46f2b86cd6f9d52
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2020
ms.locfileid: "85117992"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建 Azure Cosmos DB 和容器

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 使用 Azure Cosmos DB，可以快速创建和查询键/值数据库、文档数据库和图形数据库。 本快速入门重点介绍了部署资源管理器模板，用以创建 Azure Cosmos 数据库以及在该数据库内创建容器的过程。 稍后你可以在该容器中存储数据。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

Azure 订阅，或免费的 Azure Cosmos DB 试用帐户

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>创建 Azure Cosmos 帐户、数据库和容器

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/)。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

该模板中定义了三个 Azure 资源：

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts)：创建 Azure Cosmos 帐户。

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases)：创建 Azure Cosmos 数据库。

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers)：创建 Azure Cosmos 容器。

可以在[快速入门模板库](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb)中找到更多 Azure Cosmos DB 模板示例。

### <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建 Azure Cosmos 帐户、数据库和容器。

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. 选择或输入以下值。

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="资源管理器模板, Azure Cosmos DB 集成, 部署门户":::

    除非另有指定，否则请使用默认值创建 Azure Cosmos 资源。

    * 订阅：选择一个 Azure 订阅。
    * 资源组：选择“新建”，输入资源组的唯一名称，然后单击“确定”。 
    * 位置：选择一个位置。  例如“美国中部”。
    * **帐户名称**：输入 Azure Cosmos 帐户的名称。 它必须全局唯一。
    * **位置**：输入要在其中创建 Azure Cosmos 帐户的位置。 Azure Cosmos 帐户必须与资源组处于同一位置。
    * **主要区域**：Azure Cosmos 帐户的主要副本区域。
    * **次要区域**：Azure Cosmos 帐户的次要副本区域。
    * **默认一致性级别**：Azure Cosmos 帐户的默认一致性级别。
    * **最大过期前缀**：最大过时请求数。 对于 BoundedStaleness 是必需的。
    * **以秒为单位的最大间隔**：最大延迟时间。 对于 BoundedStaleness 是必需的。
    * **数据库名称**：Azure Cosmos 数据库的名称。
    * **容器名称**：Azure Cosmos 容器的名称。
    * **吞吐量**：容器的吞吐量，最小吞吐量值为 400 RU/秒。
    * **我同意上述条款和条件**：选中。

3. 选择“购买”。 成功部署 Azure Cosmos 帐户后，你会收到通知：

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="资源管理器模板, Cosmos DB 集成, 部署门户通知":::

使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="validate-the-deployment"></a>验证部署

可以使用 Azure 门户检查 Azure Cosmos 帐户、数据库和容器，或者使用以下 Azure CLI 或 Azure PowerShell 脚本列出创建的机密。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组，可以将其删除，这将删除 Azure Cosmos 帐户和相关的资源。 使用 Azure CLI 或 Azure PowerShell 删除资源组：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Azure 资源管理器模板创建了 Azure Cosmos 帐户、数据库和容器，并验证了部署。 若要详细了解 Azure Cosmos DB 和 Azure 资源管理器，请继续阅读以下文章。

- 阅读 [Azure Cosmos DB 概述](introduction.md)
- 了解有关 [Azure 资源管理器](../azure-resource-manager/management/overview.md)的详细信息
- 获取其他 [Azure Cosmos DB 资源管理器模板](resource-manager-samples.md)
