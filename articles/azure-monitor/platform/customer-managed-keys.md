---
title: Azure Monitor 客户管理的密钥
description: 使用 Azure Key Vault 密钥配置客户管理的密钥 (CMK) 以加密 Log Analytics 工作区中的数据的信息和步骤。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 09/09/2020
ms.openlocfilehash: 532d96163e2ec66730dc3fdf87f10904fd584224
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107991"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 客户管理的密钥 

本文介绍了为 Log Analytics 工作区配置客户管理的密钥 (CMK) 的背景信息和步骤。 配置完成后，发送到工作区的任何数据都将使用 Azure Key Vault 密钥进行加密。

建议在配置之前，查看下方的[限制和约束](#limitationsandconstraints)。

## <a name="customer-managed-key-cmk-overview"></a>客户管理的密钥 (CMK) 概述

[静态加密](../../security/fundamentals/encryption-atrest.md) 是组织中常见的隐私和安全要求。  可以让 Azure 完全管理静态加密，同时可以使用各种选项严格管理加密或加密密钥。

Azure Monitor 确保所有数据和保存的查询都使用 Microsoft 托管的密钥 (MMK) 进行静态加密。 Azure Monitor 还提供了使用自己的密钥进行加密的选项，该密钥存储在你的 [Azure Key Vault](../../key-vault/general/overview.md) 中，并由存储使用系统分配的 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md) 身份验证来访问。 此密钥 (CMK) 可以是 [软件或硬件 HSM 保护的](../../key-vault/general/overview.md)。 Azure Monitor 使用加密与 [Azure 存储加密](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) 的运行方式相同。

CMK 功能在专用 Log Analytics 群集上提供，并使你可以随时撤销对数据的访问权限，并使用 [密码箱](#customer-lockbox-preview) 控制来保护数据。 若要验证是否已在区域中拥有专用群集所需的容量，我们需要事先允许你的订阅。 开始配置 CMK 之前，请使用你的 Microsoft 联系人获取订阅。

[Log Analytics 群集定价模型](./manage-cost-storage.md#log-analytics-dedicated-clusters)使用从 1000 GB/天级别开始的容量预留。

过去 14 天内引入的数据也保存在热缓存（受 SSD 支持）中，以实现高效的查询引擎操作。 此数据将通过 Microsoft 密钥保持加密，而不考虑 CMK 配置，但对 SSD 数据的控制将遵循 [密钥吊销](#cmk-kek-revocation)。 我们正致力于在 2020 的下半年使用 CMK 加密 SSD 数据。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure Monitor 中的工作原理

Azure Monitor 利用系统分配的托管标识授予对 Azure Key Vault 的访问权限。 系统分配的托管标识只能与单个 Azure 资源关联，而 Log Analytics 群集的标识在群集级别受支持，这就规定了 CMK 功能是在专用 Log Analytics 群集上提供的。 为了在多个工作区上支持 CMK，新的 Log Analytics 群集资源将用作 Key Vault 和 Log Analytics 工作区之间的中间标识连接。 Log Analytics 群集存储使用与群集资源关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 进行身份验证。 

CMK 配置后，链接到专用群集的任何工作区的任何数据都将在 Key Vault 中通过密钥进行加密。 你可以随时取消工作区与群集的链接。 新数据将引入到 Log Analytics 存储，并使用 Microsoft 密钥进行加密，同时你可以无缝查询新旧数据。


![CMK 概述](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics 群集资源具有拥有 Key Vault 权限的托管标识，此标识可传播到基础专用 Log Analytics 群集存储
3. 专用 Log Analytics 群集
4. 链接到 CMK 加密的 *群集* 资源的工作区

## <a name="encryption-keys-operation"></a>加密密钥操作

存储数据加密涉及 3 种类型的密钥：

- **KEK** - 密钥加密密钥 (CMK)
- **AEK** - 帐户加密密钥
- **DEK** - 数据加密密钥

下列规则适用：

- Log Analytics 群集存储帐户为每个存储帐户生成唯一的加密密钥，称为 AEK。

- AEK 用于派生 DEK 密钥，后者用于对写入磁盘的每个数据块进行加密。

- 在 Key Vault 中配置密钥并在群集中引用密钥时，Azure 存储向 Azure Key Vault 发送请求，以包装和解包 AEK 以执行数据加密和解密操作。

- KEK 绝不会离开 Key Vault，HSM 密钥绝不会离开硬件。

- Azure 存储使用与 *群集* 资源关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 进行身份验证和访问。

## <a name="cmk-provisioning-procedure"></a>CMK 预配过程

1. 允许订阅--CMK 功能在专用 Log Analytics 群集上传递。 若要验证在你的区域中是否有所需的容量，我们需要事先允许你的订阅。 使用你的 Microsoft 联系人获取允许的订阅。
2. 创建 Azure Key Vault 和存储密钥
3. 正在创建群集
4. 向 Key Vault 授予权限
5. 链接 Log Analytics 工作区

Azure 门户中不支持 CMK 配置，并且预配是通过 [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/)、 [CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics) 或 [REST](https://docs.microsoft.com/rest/api/loganalytics/) 请求执行的。

### <a name="asynchronous-operations-and-status-check"></a>异步操作和状态检查

某些配置步骤是异步运行的，因为它们无法快速完成。 在配置中使用 REST 请求时，响应最初返回 HTTP 状态代码 200 (OK) 并在接受时使用 *Azure AsyncOperation* 属性标头：
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

然后，可以通过将 GET 请求发送到 *AsyncOperation* 标头值来检查异步操作的状态：
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

响应包含有关操作及其状态的信息。 可以是以下之一：

正在执行操作
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

正在执行密钥标识符更新操作
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

正在进行群集删除--删除具有链接工作区的群集时，将为每个工作区异步执行取消链接操作，操作可能需要一段时间。
当你删除没有链接工作区的群集时，这并不相关，在这种情况下，将立即删除群集。
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作完成
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作失败
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>允许 CMK 部署的订阅

CMK 功能是在专用 Log Analytics 群集上提供的。若要验证在你的区域中是否有所需的容量，我们需要事先允许你的订阅。 在 Microsoft 中使用联系人提供订阅 ID。

> [!IMPORTANT]
> CMK 功能是区域性的。 Azure Key Vault、群集和链接 Log Analytics 工作区必须位于同一区域中，但它们可以位于不同的订阅中。

### <a name="storing-encryption-key-kek"></a>存储加密密钥 (KEK)

创建或使用已有的 Azure Key Vault，以生成或导入用于数据加密的密钥。 必须将 Azure Key Vault 配置为可恢复，以保护密钥以及对 Azure Monitor 中的数据的访问权限。 可以验证是否应启用 Key Vault 中“软删除”和“清除保护”属性下的此配置 。

![软删除和清除保护设置](media/customer-managed-keys/soft-purge-protection.png)

可以通过 CLI 和 PowerShell 更新这些设置：

- [软删除](../../key-vault/general/soft-delete-overview.md)
- [清除保护](../../key-vault/general/soft-delete-overview.md#purge-protection)可防止在软删除后强行删除机密/保管库

### <a name="create-cluster"></a>创建群集

按照 [专用群集一文](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster)中所述的过程进行操作。 

> [!IMPORTANT]
> 复制并保存响应，因为你在后续步骤中需要详细信息。

### <a name="grant-key-vault-permissions"></a>授予 Key Vault 权限

使用新的访问策略更新 Key Vault，以向群集授予权限。 基础 Azure Monitor 存储使用这些权限进行数据加密。 在 Azure 门户中打开 Key Vault，单击“访问策略”，然后单击“+ 添加访问策略”以使用以下设置创建策略：

- 密钥权限：选择“获取”、“包装密钥”和“解包密钥”权限。
- 选择主体：输入在上一步的响应中返回的群集名称或主体 id 值。

![授予 Key Vault 权限](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要“获取”权限，才能验证是否已将 Key Vault 配置为可恢复以保护密钥以及对 Azure Monitor 数据的访问。

### <a name="update-cluster-with-key-identifier-details"></a>更新具有密钥标识符详细信息的群集

群集上的所有操作都需要 Microsoft.operationalinsights/群集/写入操作权限。 可以通过包含/write 操作的所有者或参与者或 *通过包含 microsoft.operationalinsights/action Log Analytics 参与者角色* 授予此权限。

此步骤将使用要用于数据加密的密钥和版本更新 Azure Monitor 存储。 更新后，将使用新密钥来包装并解包存储密钥 (AEK) 。

在 Azure Key Vault 中选择密钥的当前版本，以获取密钥标识符详细信息。

![授予 Key Vault 权限](media/customer-managed-keys/key-identifier-8bit.png)

在具有密钥标识符详细信息的群集中更新 KeyVaultProperties。

此操作是异步操作，可能需要一段时间才能完成。

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> 可以使用 PATCH 更新群集的 *sku*、 *keyVaultProperties* 或 *billingType* 。

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

**响应**

200 OK 和标头。
完成密钥标识符的传播需要几分钟。 可以通过两种方式检查更新状态：
1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 在群集上发送 GET 请求，并查看 *KeyVaultProperties* 属性。 你最近更新的密钥标识符详细信息应返回到响应中。

完成密钥标识符更新后，对 GET 请求的响应应如下所示：

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="link-workspace-to-cluster"></a>将工作区链接到群集

需要对工作区和群集具有 "写入" 权限才能执行此操作，包括以下操作：

- 在工作区中：Microsoft.OperationalInsights/workspaces/write
- 在群集中： Microsoft.operationalinsights/群集/写入

> [!IMPORTANT]
> 完成 Log Analytics 群集的预配后才应执行此步骤。 如果在设置之前链接工作区和引入数据，引入数据将被删除，并且将无法恢复。

此操作是异步操作，可能需要一段时间才能完成。

按照 [专用群集一文](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster)中所述的过程进行操作。

## <a name="cmk-kek-revocation"></a>CMK (KEK) 吊销

可以通过禁用密钥，或在 Key Vault 中删除群集的访问策略，来撤销对数据的访问权限。 Log Analytics 群集存储在一小时或更短时间内将始终遵循关键权限的更改，并且存储将变得不可用。 与群集链接的工作区的任何新数据引入会被删除且不可恢复，数据不可访问，对这些工作区的查询会失败。 以前引入数据将保留在存储中，只要群集和工作区不会被删除。 不可访问的数据由数据保留策略管理，并在保留期截止时被清除。 

过去 14 天内引入的数据也保存在热缓存（SSD 提供支持）中，以实现高效的查询引擎操作。 它将在进行密钥吊销操作后被删除，并且也变得不可访问。

存储会定期轮询 Key Vault 以尝试解包加密密钥，数据引入和查询将在受到访问后的 30 分钟内恢复。

## <a name="cmk-kek-rotation"></a>CMK (KEK) 轮换

CMK 的旋转需要在 Azure Key Vault 中用新的密钥版本显式更新群集。 按照 "通过密钥标识符详细信息更新群集" 步骤中的说明进行操作。 如果未更新群集中的新密钥标识符详细信息，Log Analytics 群集存储将继续使用以前的密钥进行加密。 如果在更新群集中的新密钥之前禁用或删除了旧密钥，则会进入 [密钥吊销](#cmk-kek-revocation) 状态。

进行密钥轮换操作后，所有数据都将保持可访问，因为数据始终使用帐户加密密钥 (AEK) 进行加密，而 AEK 目前使用 Key Vault 中的新密钥加密密钥 (KEK) 版本进行加密。

## <a name="cmk-for-queries"></a>查询的 CMK

Log Analytics 中使用的查询语言是有意义的，可以在添加到查询或查询语法中的注释中包含敏感信息。 某些组织要求将此类信息作为 CMK 策略的一部分进行保护，并且需要保存用密钥加密的查询。 Azure Monitor 使你能够在连接到工作区时，将 *已保存的搜索* 和 *日志警报* 查询存储在你自己的存储帐户中。 

> [!NOTE]
> Log Analytics 查询可以保存在不同的存储中，具体取决于所使用的方案。 无论 CMK 配置： Azure Monitor、Azure 仪表板、Azure 逻辑应用、Azure Notebooks 和自动化 Runbook 中的工作簿，在以下情况下，查询将在 Microsoft key (MMK) 保持加密。

当你将自己的存储 (BYOS) 并将其链接到你的工作区时，该服务会将 *已保存的搜索* 和 *日志警报* 查询上载到你的存储帐户。 这意味着，你可以使用与加密 Log Analytics 群集中的数据或其他密钥相同的密钥来控制存储帐户和 [静态加密策略](../../storage/common/customer-managed-keys-overview.md) 。 但是，你将负责与该存储帐户关联的成本。 

**为查询设置 CMK 前的注意事项**
* 你需要对工作区和存储帐户具有 "写入" 权限
* 请确保在 Log Analytics 工作区所在的同一区域中创建存储帐户
* 存储中的 *保存搜索* 被视为服务项目，其格式可能会更改
* 现有的 *保存搜索* 将从工作区中删除。 复制并在配置之前保存所需的任何 *搜索* 。 可以使用[PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)查看*已保存的搜索*
* 查询历史记录不受支持，您将无法看到您运行的查询
* 可以将单个存储帐户链接到工作区以便保存查询，但可用于 *保存的搜索* 和 *日志警报* 查询
* 不支持固定到仪表板

**为保存的搜索查询配置 BYOS**

将 *查询* 的存储帐户链接到工作区- *保存-搜索* 查询保存在存储帐户中。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

配置完成后，任何新 *保存的搜索* 查询都将保存在存储中。

**配置 BYOS 查询的日志记录**

将 *警报* 的存储帐户链接到工作区-- *日志警报* 查询保存在存储帐户中。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

配置后，任何新的警报查询都将保存在存储中。

## <a name="customer-lockbox-preview"></a>客户密码箱 (预览版) 
密码箱使你可以在支持请求期间批准或拒绝 Microsoft 工程师请求访问你的数据。

在 Azure Monitor 中，对链接到 Log Analytics 专用群集的工作区中的数据具有此控制。 密码箱控件适用于存储在 Log Analytics 专用群集中的数据，在此群集中，它在受密码保护的订阅下群集的存储帐户中保持隔离。  

了解有关[Microsoft Azure 客户密码箱的](../../security/fundamentals/customer-lockbox-overview.md)详细信息

## <a name="cmk-management"></a>CMK 管理

- **获取资源组中的所有群集**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **获取订阅中的所有群集**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **响应**
    
  与在 "资源组" 中的 "群集" 相同的响应，但在订阅范围内。

- **更新群集中的*容量预留***

  当链接工作区的数据量随着时间推移而更改，并且你想要适当地更新产能预留级别时。 按照 [更新群集](#update-cluster-with-key-identifier-details) 操作，提供新的容量价值。 它可以在1000到 3000 GB 的范围内，在100的范围内。 如果每日级别高于 3000 GB，请联系你的 Microsoft 联系人以启用它。 请注意，不需要提供完整的 REST 请求正文，而应包含 sku：

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **更新群集中的*billingType***

  *BillingType*属性确定群集及其数据的计费归属：
  - 群集（默认）-- 计费归属于承载群集资源的订阅
  - 工作区 -- 计费按比例归属于承载工作区的订阅
  
  按照 [更新群集](#update-cluster-with-key-identifier-details) 操作并提供新的 billingType 值。 请注意，无需提供完整的 REST 请求正文，但应包含 billingType：

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **取消链接工作区**

  需要对工作区和群集具有 "写入" 权限才能执行此操作。 你随时可以从群集中取消链接工作区。 取消链接操作后的新引入数据存储在 Log Analytics 存储中，并通过 Microsoft 密钥进行加密。 只要预配和配置了有效 Key Vault 密钥，就可以在无缝取消链接之前和之后查询已引入到工作区的数据。

  此操作是异步操作，可能需要一段时间才能完成。

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**

  200 OK 和标头。

  取消链接操作后的引入数据存储在 Log Analytics 存储中，这可能需要90分钟才能完成。 可以通过两种方式检查工作区取消链接状态：

  1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
  2. 发送[工作区– Get](/rest/api/loganalytics/workspaces/get)请求并观察响应，未链接的工作区在*功能*下将不具有*clusterResourceId* 。

- **检查工作区链接状态**
  
  对工作区执行 Get 操作，并观察 "*功能*" 下的响应中是否存在*clusterResourceId*属性。 链接的工作区将具有 *clusterResourceId* 属性。

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **删除群集**

  需要对群集具有 "写入" 权限才能执行此操作。 执行软删除操作可以在14天内恢复你的群集（包括其数据），不管是有意还是无意删除。 群集名称在软删除期间仍然保留，因此无法使用该名称创建新群集。 软删除期结束后，群集名称将被释放，并且群集及其数据将被永久删除且不可恢复。 删除操作时，任何链接的工作区都将从群集中取消链接。 新引入数据存储在 Log Analytics 存储中，并使用 Microsoft 密钥进行加密。 
  
  取消链接操作是异步的，最长可能需要90分钟才能完成。

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常

- **恢复群集和数据** 
  
  在过去14天内删除的群集处于软删除状态，可以使用其数据进行恢复。 由于所有工作区都在删除时未与群集断开连接，因此需要在恢复 CMK 加密后重新链接工作区。 当前，恢复操作由产品组手动执行。 使用 Microsoft 通道实现恢复请求。

## <a name="limitationsandconstraints"></a>限制和约束

- CMK 支持专用 Log Analytics 群集，适用于每天发送1TB 或更多的客户。

- 每个区域和订阅的群集的最大数目为2

与群集关联的最大工作区为100

- 可以将工作区链接到群集，然后在工作区不需要 CMK 时取消链接。 特定工作区的工作区链接操作数在30天内限制为2。

- 只有在验证 Log Analytics 群集预配完成后，才能执行到群集的工作区链接。  完成预配之前发送到工作区的数据将被删除，并且无法恢复。

- CMK 加密适用于 CMK 配置后的新引入数据。  CMK 配置之前引入的数据仍将使用 Microsoft 密钥进行加密。  你可以无缝查询 CMK 配置前后的数据。

- 必须将 Azure Key Vault 配置为可恢复。 默认情况下，这些属性不会启用，应该使用 CLI 或 PowerShell 进行配置：<br>
  - [软删除](../../key-vault/general/soft-delete-overview.md)
  - 即使在软删除后，也应打开[清除保护](../../key-vault/general/soft-delete-overview.md#purge-protection)，以防强制删除密钥/保管库。

- 目前不支持群集移动到另一个资源组或订阅。

- Azure Key Vault、群集和链接的工作区必须位于同一区域，并且在同一 Azure Active Directory 中 (Azure AD) 租户，但它们可以在不同的订阅中。

- 如果链接到其他群集，则工作区链接到群集将失败

## <a name="troubleshooting"></a>疑难解答

- Key Vault 可用性的行为
  - 在正常操作中，存储会缓存 AEK 一小段时间，并返回 Key Vault 定期进行解包。
    
  - 暂时性连接错误 -- 存储通过允许密钥在缓存中保留一小段时间来处理暂时性错误（超时、连接失败、DNS 问题），这可以克服可用性方面的任何小问题。 查询和引入功能将继续运行而不会中断。
    
  - 实时网站 -- 如果在约 30 分钟内未进行访问，则会导致无法使用存储帐户。 查询功能不可用，引入的数据会使用 Microsoft 密钥缓存几个小时，以避免数据丢失。 恢复对 Key Vault 的访问后，查询将变为可用，临时缓存的数据将引入到数据存储并使用 CMK 进行加密。

  - Key Vault 访问速率 -- Azure Monitor 存储为实现包装和解包操作而访问 Key Vault 的频率介于 6 到 60 秒之间。

- 如果创建群集并立即指定 KeyVaultProperties，则操作可能会失败，因为在向群集分配系统标识之前无法定义访问策略。

- 如果使用 KeyVaultProperties 更新现有群集并且 Key Vault 中缺少 "Get" 密钥访问策略，则操作将失败。

- 如果在创建群集时出现冲突，则可能是你在过去14天内删除了群集，并且该群集位于软删除期间。 群集名称在软删除期间仍然保留，因此无法使用该名称创建新群集。 在永久删除群集后，该名称将在软删除期间释放。

- 如果在操作过程中更新群集，操作将失败。

- 如果无法部署群集，请验证 Azure Key Vault、群集和链接 Log Analytics 工作区是否位于同一区域。 可以位于不同的订阅。

- 如果你 Key Vault 更新密钥版本，但未更新群集中的新密钥标识符详细信息，Log Analytics 群集将继续使用以前的密钥，你的数据将变得不可访问。 更新群集中的新密钥标识符详细信息，恢复数据引入和查询数据。

- 某些操作很长，可能需要一段时间才能完成，这些操作包括群集创建、群集密钥更新和群集删除。 可以通过两种方式检查操作状态：
  1. 使用 REST 时，从响应中复制 "Azure-AsyncOperation URL" 值并按照 [异步操作状态检查操作](#asynchronous-operations-and-status-check)。
  2. 向群集或工作区发送 GET 请求并观察响应。 例如，未链接的工作区在*功能*下将不具有*clusterResourceId* 。

- 与客户管理的密钥相关的支持和帮助，请在 Microsoft 中使用你的联系人信息。

- Error messages
  
  群集创建：
  -  400--群集名称无效。 群集名称可以包含字符 a-z、a-z、0-9 和长度3-63。
  -  400--请求正文为 null 或格式不正确。
  -  400--SKU 名称无效。 将 SKU 名称设置为 capacityReservation。
  -  400-已提供容量，但 SKU 不可 capacityReservation。 将 SKU 名称设置为 capacityReservation。
  -  400--SKU 中缺少容量。 在 100 (GB) 的步骤中，将 "容量" 值设置为1000或更高。
  -  400--SKU 中的容量不在范围内。 应为最小1000，最大可达工作区中 "使用量和预估成本" 下提供的最大容量。
  -  400--容量已锁定30天。 更新后30天内允许减少容量。
  -  400--未设置 SKU。 在 100 (GB) 的步骤中，将 SKU 名称设置为 capacityReservation，并将容量值设置为1000或更高。
  -  400--Identity 为 null 或为空。 设置 systemAssigned 类型的标识。
  -  400--KeyVaultProperties 在创建时设置。 群集创建后更新 KeyVaultProperties。
  -  400--操作无法立即执行。 异步操作的状态不是 "成功"。 群集必须在执行任何更新操作之前完成其操作。

  群集更新
  -  400--群集处于删除状态。 正在进行异步操作。 群集必须在执行任何更新操作之前完成其操作。
  -  400--KeyVaultProperties 不为空，但格式不正确。 请参阅 [密钥标识符更新](#update-cluster-with-key-identifier-details)。
  -  400--未能验证 Key Vault 中的密钥。 可能是由于权限不足或键不存在。 验证是否在 Key Vault 中 [设置了密钥和访问策略](#grant-key-vault-permissions) 。
  -  400--密钥不可恢复。 必须将 "Key Vault" 设置为 "软删除" 和 "清除保护"。 请参阅 [Key Vault 文档](../../key-vault/general/soft-delete-overview.md)
  -  400--操作无法立即执行。 等待异步操作完成，然后重试。
  -  400--群集处于删除状态。 等待异步操作完成，然后重试。

  群集 Get：
    -  404--找不到群集，群集可能已被删除。 如果尝试创建具有该名称的群集并且发生冲突，则群集在软删除中14天。 你可以联系支持人员来恢复它，或使用其他名称创建一个新群集。 

  群集删除
    -  409--在处于预配状态时无法删除群集。 等待异步操作完成，然后重试。

  工作区链接：
  -  404--找不到工作区。 您指定的工作区不存在或已被删除。
  -  409--工作区链接或取消链接操作正在处理中。
  -  400--找不到群集，你指定的群集不存在或已被删除。 如果尝试创建具有该名称的群集并且发生冲突，则群集在软删除中14天。 可以联系支持人员来恢复。

  工作区取消链接：
  -  404--找不到工作区。 您指定的工作区不存在或已被删除。
  -  409--工作区链接或取消链接操作正在处理中。
