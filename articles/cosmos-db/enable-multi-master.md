---
title: 为 Azure Cosmos DB 帐户启用多主数据库
description: 本文介绍如何在使用 Azure 门户、PowerShell、CLI 或 Azure 资源管理器模板创建 Azure Cosmos DB 帐户时启用多主数据库支持。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b6fc04fc728f753dc8a3900b26c6c01f03cc7710
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077232"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>为 Azure Cosmos DB 帐户启用多主数据库

创建 Azure Cosmos DB 帐户时启用多主数据库支持。 可以使用 Azure 门户、PowerShell、CLI 或 Azure 资源管理器模板创建 Azure Cosmos DB 帐户。

> [!IMPORTANT]
> 当前只能为新的 Azure Cosmos DB 帐户启用多主数据库支持。 现有 Azure Cosmos DB 帐户不能使用该功能。 我们正在致力于为现有帐户提供支持，可用时将宣布此支持。

创建具有多主数据库支持的 Azure Cosmos DB 帐户后，可以创建数据库和容器、上传文档，并分配冲突解决策略。 有关多主数据库和代码示例中的冲突解决信息，请参阅[多主数据库代码示例](multi-master-conflict-resolution.md#code-samples)一文。

## <a name="enable-multi-master-using-azure-portal"></a>使用 Azure 门户启用多主数据库

1. 登录到 [Azure 门户](https://portal.azure.com/)

2. 单击并选择“创建资源”>“数据库”>“Azure Cosmos DB”。

3. 在“新建帐户”窗格中，输入新 Azure Cosmos DB 帐户的以下设置：

   |**设置**  |建议的值 |**说明**|
   |---------|---------|---------|
   |订阅   | {你的订阅}  |选择要用于此 Azure Cosmos DB 帐户的 Azure 订阅。  |
   |资源组  |   {你的资源组名称}    |  选择现有资源组，或选择  **新建** ，然后输入帐户的新资源组名称。 |
   |帐户名 | {你的帐户名}   |  输入标识此 Azure Cosmos DB 帐户的唯一名称。        |
   |API  |   任意   |  选择 API 类型。   |
   |位置  | 选择任何区域   | 选择要在其中托管 Azure Cosmos DB 帐户的地理位置。 此帐户将位于多个区域中，因此可以选择任何区域。  |
   |启用异地冗余   |  启用  |  选择以启用下方所选的多主数据库。   |
   |启用多主数据库 | 启用  | 选择以为此帐户启用多主数据库。 |


## <a name="using-multi-master-in-sdks"></a>在 SDK 中使用多主数据库

使用启用了多主数据库的帐户，在应用程序中，可通过利用如下所示的 ConnectionPolicy 来使用多主数据库。

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add(LocationNames.WestUS);
policy.PreferredLocations.Add(LocationNames.NorthEurope);
policy.PreferredLocations.Add(LocationNames.SoutheastAsia);
```

## <a name="enable-multi-master-using-powershell"></a>使用 PowerShell 启用多主数据库

此外，通过将 `enableMultipleWriteLocations` 参数为“true”，还可创建启用了多主数据库的 Cosmos DB 帐户。 若要创建启用了多主数据库的 Cosmos DB 帐户，请打开 PowerShell 窗口，并运行以下脚本：

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>使用 CLI 启用多主数据库

可通过将 enable-multiple-write-locations 参数设置为“true”启用多主数据库。 若要创建启用了多主数据库的 Cosmos DB 帐户，请打开 Azure CLI 或云 shell，并运行以下命令：

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>使用资源管理器模板启用多主数据库

以下 JSON 代码是可用于部署 Azure Cosmos DB 帐户的示例资源管理器模板。 如需了解资源管理器模板的格式和语法，请参阅[资源管理器](../azure-resource-manager/resource-group-authoring-templates.md)文档。 此模板中需要注意的关键参数为 "enableMultipleWriteLocations": true。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何为 Azure Cosmos DB 帐户启用多主数据库支持。 接下来请浏览以下资源：

* [将多主数据库与开放源代码 NoSQL 数据库结合使用](multi-master-oss-nosql.md)

* [了解 Azure Cosmos DB 中的冲突解决](multi-master-conflict-resolution.md)
