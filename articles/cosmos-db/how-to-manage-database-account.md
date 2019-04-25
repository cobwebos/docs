---
title: 了解如何在 Azure Cosmos DB 中管理数据库帐户
description: 了解如何在 Azure Cosmos DB 中管理数据库帐户
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: b2b5e58ca480aa3abaa0766319977b8d1160ebeb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282995"
---
# <a name="manage-an-azure-cosmos-account"></a>管理 Azure Cosmos 帐户

本文介绍了如何管理 Azure Cosmos 帐户。 你将了解如何设置多宿主功能、添加或删除区域、配置多个写入区域，以及设置故障转移优先级。 

## <a name="create-a-database-account"></a>创建数据库帐户

### <a id="create-database-account-via-portal"></a>Azure 门户

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>配置多宿主客户端

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>.NET SDK v3（预览版）

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java 异步 SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>在数据库帐户中添加/删除区域

### <a id="add-remove-regions-via-portal"></a>Azure 门户

1. 导航到 Azure Cosmos 帐户，打开“全局复制数据”菜单。

2. 若要添加区域，请在地图上选择带有 **+** 标签且与你所需的区域对应的六边形。 另外，若要添加某个区域，请选择“+ 添加区域”选项，然后从下拉菜单中选择一个区域。

3. 若要删除区域，请选择带对号的蓝色六边形以从地图中清除一个或多个区域。 或者选择右侧位于区域旁边的“废纸篓”(🗑) 图标。

4. 若要保存更改，请选择“确定”。

   ![添加或删除区域菜单](./media/how-to-manage-database-account/add-region.png)

在单区域写入模式下，不能删除写入区域。 必须先故障转移到另一区域，然后才能删除当前的写入区域。

在多区域写入模式下，如果你至少具有一个区域，则可以添加或删除任何区域。

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Create an account with 1 region
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
```

## <a name="configure-multiple-write-regions"></a>配置多个写入区域

### <a id="configure-multiple-write-regions-portal"></a>Azure 门户

创建数据库帐户时，请确保启用“多区域写入”设置。

![Azure Cosmos 帐户创建屏幕截图](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>资源管理器模板

以下 JSON 代码是 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模板的一个示例。 可以使用它部署采用[有限过期一致性级别](consistency-levels.md)的 Azure Cosmos 帐户。 最大过期时间间隔设置为 5 秒。 可以容忍的过期请求的最大数量设置为 100。 若要了解资源管理器模板的格式和语法，请参阅[资源管理器](../azure-resource-manager/resource-group-authoring-templates.md)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
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


## <a id="manual-failover"></a>为 Azure Cosmos 帐户启用手动故障转移

### <a id="enable-manual-failover-via-portal"></a>Azure 门户

1. 导航到 Azure Cosmos 帐户，打开“全局复制数据”菜单。

2. 在菜单顶部，选择“手动故障转移”。

   ![“全局复制数据”菜单](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在“手动故障转移”菜单上，选择你的新写入区域。 选中相应的复选框，以指示你了解此选项会更改你的写入区域。

4. 若要触发故障转移，请选择“确定”。

   ![手动故障转移门户菜单](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
```

## <a id="automatic-failover"></a>为 Azure Cosmos DB 帐户启用自动故障转移

### <a id="enable-automatic-failover-via-portal"></a>Azure 门户

1. 在 Azure Cosmos DB 帐户中，打开“全局复制数据”窗格。 

2. 在窗格顶部选择“自动故障转移”。

   ![“全局复制数据”菜单](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在“自动故障转移”窗格中，确保将“启用自动故障转移”设置为“开”。 

4. 选择“保存”。

   ![自动故障转移门户菜单](./media/how-to-manage-database-account/automatic-failover.png)

也可在此菜单上设置自动故障转移优先级。

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>为 Azure Cosmos 帐户设置故障转移优先级

### <a id="set-failover-priorities-via-portal"></a>Azure 门户

1. 在 Azure Cosmos 帐户中，打开“全局复制数据”窗格。 

2. 在窗格顶部选择“自动故障转移”。

   ![“全局复制数据”菜单](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在“自动故障转移”窗格中，确保将“启用自动故障转移”设置为“开”。 

4. 若要修改故障转移优先级，请将鼠标指针悬停在读取区域上，并通过在行左侧出现的三个点拖动读取区域。 

5. 选择“保存”。

   ![自动故障转移门户菜单](./media/how-to-manage-database-account/automatic-failover.png)

不能在此菜单上修改写入区域。 若要手动更改写入区域，必须执行手动故障转移。

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>后续步骤

请阅读以下文章：

* [管理一致性](how-to-manage-consistency.md)
* [管理区域之间的冲突](how-to-manage-conflicts.md)
* [全球分布 - 揭秘](global-dist-under-the-hood.md)
* [如何配置应用程序中的多主数据库](how-to-multi-master.md)
* [配置多宿主客户端](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [在 Azure Cosmos DB 帐户中添加或删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [创建自定义冲突解决策略](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

