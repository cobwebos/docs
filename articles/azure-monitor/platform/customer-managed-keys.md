---
title: Azure Monitor 客户管理的密钥
description: 使用 Azure Key Vault 密钥配置客户管理的密钥 (CMK) 以加密 Log Analytics 工作区中的数据的信息和步骤。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/20/2020
ms.openlocfilehash: 037edb8af6e04a2ff65977a92a66482c9f4f880f
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845092"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 客户管理的密钥 

本文介绍了为 Log Analytics 工作区配置客户管理的密钥 (CMK) 的背景信息和步骤。 配置完成后，发送到工作区的任何数据都将使用 Azure Key Vault 密钥进行加密。

建议在配置之前，查看下方的[限制和约束](#limitationsandconstraints)。

## <a name="customer-managed-key-cmk-overview"></a>客户管理的密钥 (CMK) 概述

[静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是组织中常见的隐私和安全要求。 可以让 Azure 完全管理静态加密，同时可以使用各种选项严格管理加密或加密密钥。

Azure Monitor 可确保使用 Azure 托管的密钥对所有数据进行静态加密。 Azure Monitor 还可以使用你自己的密钥进行数据加密，该密钥存储在  [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 中，并且存储可通过系统分配的 [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)身份验证对其进行访问。 此密钥可以 [受软件保护或受硬件 HSM 保护](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

Azure Monitor 进行加密的操作与 [Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)的操作相同。

CMK 允许你控制对数据的访问，并允许随时撤消访问。 Azure Monitor 存储始终会在一小时内遵从关键权限更改。 过去 14 天内引入的数据也保存在热缓存（受 SSD 支持）中，以实现高效的查询引擎操作。 此数据使用 Microsoft 密钥进行加密，而不考虑 CMK 配置，但你对 SSD 数据的控制将遵循 [密钥吊销](#cmk-kek-revocation)。 我们正致力于在 2020 的下半年使用 CMK 加密 SSD 数据。

CMK 功能是在专用 Log Analytics 群集上提供的。 为验证我们在你所在区域具有所需的容量，我们要求你的订阅已事先列入白名单。 开始配置 CMK 之前，请使用 Microsoft 联系人获取已加入白名单的订阅。

 [Log Analytics 群集定价模型](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)使用 1000 GB/天起的容量预留。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure Monitor 中的工作原理

Azure Monitor 利用系统分配的托管标识授予对 Azure Key Vault 的访问权限。 系统分配的托管标识只能与单个 Azure 资源关联，而 Log Analytics 群集的标识在群集级别受支持，这就规定了 CMK 功能是在专用 Log Analytics 群集上提供的。 为了在多个工作区上支持 CMK，新的 Log Analytics 群集资源将用作 Key Vault 和 Log Analytics 工作区之间的中间标识连接。 Log Analytics 群集存储使用与群集资源关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 进行身份验证。 

CMK 配置完成后，与群集资源关联的工作区中引入的所有数据都将使用 Key Vault 中的密钥进行加密。 你可以随时取消工作区与群集资源的关联。 新数据将引入到 Log Analytics 存储，并使用 Microsoft 密钥进行加密，同时你可以无缝查询新旧数据。


![CMK 概述](media/customer-managed-keys/cmk-overview-8bit.png)

1. Key Vault
2. Log Analytics 群集资源具有拥有 Key Vault 权限的托管标识，此标识可传播到基础专用 Log Analytics 群集存储
3. 专用 Log Analytics 群集
4. 与用于 CMK 加密的群集资源关联的工作区

## <a name="encryption-keys-operation"></a>加密密钥操作

存储数据加密涉及 3 种类型的密钥：

- **KEK** - 密钥加密密钥 (CMK)
- **AEK** - 帐户加密密钥
- **DEK** - 数据加密密钥

下列规则适用：

- Log Analytics 群集存储帐户为每个存储帐户生成唯一的加密密钥，称为 AEK。

- AEK 用于派生 DEK 密钥，后者用于对写入磁盘的每个数据块进行加密。

- 在 Key Vault 中配置密钥并在群集资源中引用它时，Azure 存储会将请求发送到 Azure Key Vault 以包装和解包 AEK，从而执行数据加密和解密操作。

- KEK 绝不会离开 Key Vault，HSM 密钥绝不会离开硬件。

- Azure 存储使用与群集资源关联的托管标识通过 Azure Active Directory 对 Azure Key Vault 进行身份验证和访问。

## <a name="cmk-provisioning-procedure"></a>CMK 预配过程

1. 订阅白名单 -- CMK 功能是在专用 Log Analytics 群集上提供的。 为验证我们在你所在区域具有所需的容量，我们要求你的订阅已事先列入白名单。 使用 Microsoft 联系人获取已加入白名单的订阅
2. 创建 Azure Key Vault 和存储密钥
3. 创建群集资源
4. 向 Key Vault 授予权限
5. 关联 Log Analytics 工作区

当前 UI 中不支持此过程，并且预配流程是通过 REST API 执行的。

> [!IMPORTANT]
> 任何 API 请求都必须在请求标头中包含持有者授权令牌。

例如：

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

其中 eyJ0eXAiO.... 表示完全授权令牌。 

可以使用以下方法之一获取该令牌：

1. 选择[应用注册](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。
2. 在 Azure 门户中
    1. 在“开发人员工具”中导航到 Azure 门户 (F12)
    1. 在“batch?api-version”实例之一中的“请求标头”下查找授权字符串。 如下所示：“authorization:Bearer eyJ0eXAiO....”。 
    1. 按照以下示例，将其复制并添加到 API 调用。
3. 导航到 Azure REST 文档站点。 在任何 API 上按“试一下”，并复制持有者令牌。

### <a name="asynchronous-operations-and-status-check"></a>异步操作和状态检查

此配置过程中的某些操作是异步运行的，因为它们无法快速完成。 接受时，异步操作的响应最初返回 HTTP 状态代码 200 (OK) 和具有 Azure-AsyncOperation 属性的标头：
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

若要查看异步操作的状态，请向 Azure-AsyncOperation 标头值发送 GET 请求：
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
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

正在删除群集资源 -- 删除具有工作区的群集资源且该工作区与工作区关联时，系统会对异步操作中的每个工作区执行解除关联操作，这可能需要一段时间 。
这种情况与删除不具有关联工作区的群集并不相关，在后一情况中，系统将立即删除群集资源 。
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

### <a name="subscription-whitelisting"></a>订阅白名单

CMK 功能是在专用 Log Analytics 群集上提供的。 为验证我们在你所在区域具有所需的容量，我们要求你的订阅已事先列入白名单。 在 Microsoft 中使用联系人提供订阅 ID。

> [!IMPORTANT]
> CMK 功能是区域性的。 Azure Key Vault、群集资源和关联的 Log Analytics 工作区必须位于同一区域，但可以位于不同订阅。

### <a name="storing-encryption-key-kek"></a>存储加密密钥 (KEK)

创建或使用已有的 Azure Key Vault，以生成或导入用于数据加密的密钥。 必须将 Azure Key Vault 配置为可恢复，以保护密钥以及对 Azure Monitor 中的数据的访问权限。 可以验证是否应启用 Key Vault 中“软删除”和“清除保护”属性下的此配置 。

![软删除和清除保护设置](media/customer-managed-keys/soft-purge-protection.png)

可以通过 CLI 和 PowerShell 使用这些设置：
- [软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)可防止在软删除后强行删除机密/保管库

### <a name="create-cluster-resource"></a>创建群集资源

此资源用作 Key Vault 与 Log Analytics 工作区之间的中间标识连接。 收到订阅已添加到白名单的确认信息后，请在工作区所在的区域创建 Log Analytics 群集资源。

创建群集资源时，必须指定容量预留级别 (sku) 。 容量预留级别可以在每天 1,000 到 2,000 GB 范围内，后续你可以以 100 为幅度对其进行更新。 如果你需要的容量预留级别高于每天 2,000 GB，请通过 LAIngestionRate@microsoft.com 与我们联系。 [了解详细信息](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

billingType 属性可确定群集资源及其数据的计费归属 ：
- 群集（默认）-- 计费归属于承载群集资源的订阅 
- 工作区 -- 计费按比例归属于承载工作区的订阅

> [!NOTE]
> 创建群集资源后，可以使用 PATCH REST 请求为其更新 sku、keyVaultProperties 或 billingType   。

**创建**

此资源管理器请求是异步操作。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

标识会在创建时分配到群集资源。

**响应**

200 OK 和标头。

完成 Log Analytics 群集的预配需要花费一段时间，你可以通过两种方式检查预配状态：

1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 在群集资源上发送 GET 请求，然后查看 provisioningState 值 。 预配进行时是 ProvisioningAccount，预配完成后是 Succeeded 。

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 复制并保存响应，因为你在后续步骤中需要详细信息。

**响应**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "clusterType": "LogAnalytics",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

“principalId”GUID 是托管标识服务为群集资源生成的。

### <a name="grant-key-vault-permissions"></a>授予 Key Vault 权限

使用新的访问策略更新 Key Vault，该策略会向群集资源授予权限。 基础 Azure Monitor 存储使用这些权限进行数据加密。 在 Azure 门户中打开 Key Vault，单击“访问策略”，然后单击“+ 添加访问策略”以使用以下设置创建策略：

- 密钥权限：选择“获取”、“包装密钥”和“解包密钥”权限。
- 选择主体：输入在上一步的响应中返回的主体 id 值。

![授予 Key Vault 权限](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要“获取”权限，才能验证是否已将 Key Vault 配置为可恢复以保护密钥以及对 Azure Monitor 数据的访问。

### <a name="update-cluster-resource-with-key-identifier-details"></a>为群集资源更新密钥标识符详细信息

此步骤会在 Key Vault 中的初始以及未来密钥版本更新期间执行。 它通知 Azure Monitor 存储有关用于数据加密的密钥版本的信息。 更新后，新密钥将用于包装和解包到存储密钥 (AEK)。

若要为群集资源更新 Key Vault 密钥标识符详细信息，请在 Azure Key Vault 中选择密钥的当前版本，以获取密钥标识符详细信息 。

![授予 Key Vault 权限](media/customer-managed-keys/key-identifier-8bit.png)

为群集资源 KeyVaultProperties 更新密钥标识符详细信息。

**Update**

此资源管理器请求在更新密钥标识符详细信息时是异步操作，在更新容量值时是同步操作。

> [!NOTE]
> 可以在群集资源中提供部分正文以更新 sku，keyVaultProperties 或 billingType   。

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

“KeyVaultProperties”包含 Key Vault 密钥标识符详细信息。

**响应**

200 OK 和标头。
完成密钥标识符的传播需要几分钟。 可以通过两种方式检查更新状态：
1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 在群集资源上发送 GET 请求，然后查看 KeyVaultProperties 属性 。 你最近更新的密钥标识符详细信息应返回到响应中。

密钥标识符更新完成后，对群集资源上 GET 请求的响应应如下所示：

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
    "clusterType": "LogAnalytics", 
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>与群集资源的工作区关联

需要具有对工作区和群集资源的“写入”权限才能执行此操作，其中包括以下操作：

- 在工作区中：Microsoft.OperationalInsights/workspaces/write
- 在群集资源中：Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> 完成 Log Analytics 群集的预配后才应执行此步骤。 如果在预配前关联工作区并引入数据，则引入的数据将被删除，并且无法恢复。

**关联工作区**

此资源管理器请求是异步操作。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**响应**

200 OK 和标头。

完成关联操作后，引入的数据会使用托管密钥进行加密存储，这可能需要长达 90 分钟才能完成。 可以通过两种方式检查工作区关联状态：

1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 发送 [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 请求并观察响应，关联工作区的“功能”下方将具有 clusterResourceId。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**响应**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>CMK (KEK) 吊销

可以通过禁用密钥，或删除 Key Vault 中的群集资源访问策略来撤销对数据的访问。 Log Analytics 群集存储在一小时或更短时间内将始终遵循关键权限的更改，并且存储将变得不可用。 与群集资源关联的工作区中引入的任何新数据都将被删除，并且无法恢复，数据将不可访问，针对这些工作区的查询将会失败 ** 。 只要不删除群集资源和工作区，之前引入的数据就会保留在存储中。 不可访问的数据由数据保留策略管理，并在保留期截止时被清除。 

过去 14 天内引入的数据也保存在热缓存（SSD 提供支持）中，以实现高效的查询引擎操作。 它将在进行密钥吊销操作后被删除，并且也变得不可访问。

存储会定期轮询 Key Vault 以尝试解包加密密钥，数据引入和查询将在受到访问后的 30 分钟内恢复。

## <a name="cmk-kek-rotation"></a>CMK (KEK) 轮换

CMK 的轮换需要使用 Azure Key Vault 中的新密钥版本对群集资源进行显式更新。 按照“为群集资源更新密钥标识符详细信息”步骤中的说明进行操作。 如果未在群集资源中更新新密钥标识符详细信息，Log Analytics 群集存储将继续使用之前的密钥进行加密。 如果在更新群集资源中的新密钥之前禁用或删除旧密钥，则你将进入[密钥吊销](#cmk-kek-revocation)状态。

进行密钥轮换操作后，所有数据都将保持可访问，因为数据始终使用帐户加密密钥 (AEK) 进行加密，而 AEK 目前使用 Key Vault 中的新密钥加密密钥 (KEK) 版本进行加密。

## <a name="cmk-manage"></a>CMK 管理

- 获取资源组的所有群集资源

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
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
          "clusterType": "LogAnalytics", 
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

- 获取订阅的所有群集资源

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **响应**
    
  与“资源组的群集资源”的响应相同，但在订阅范围内。

- 更新群集资源中的容量预留 

  关联工作区的数据量随时间变化时，建议适当地更新容量预留级别。 按照[更新群集资源](#update-cluster-resource-with-key-identifier-details)中的步骤进行操作，并提供新的容量值。 它可以在每天 1,000 到 2,000 GB 范围内，并以 100 为度。 如果级别高于每日 2,000 GB，请联系 Microsoft 联系人实现该级别。 请注意，无需提供完整的 REST 请求正文，但应包含 sku：

  **body**
  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- 更新群集资源中的 billingType 

  billingType 属性可确定群集资源及其数据的计费归属 ：
  - 群集（默认）-- 计费归属于承载群集资源的订阅
  - 工作区 -- 计费按比例归属于承载工作区的订阅
  
  按照[更新群集资源](#update-cluster-resource-with-key-identifier-details)中的步骤进行操作，并提供新的 billingType 值。 请注意，无需提供完整的 REST 请求正文，但应包含 billingType：

  **body**
  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **解除关联工作区**

  你需要对工作区具有“写入”权限，并且需要群集资源才能执行此操作。 你可以随时取消工作区与群集资源的关联。 进行解除关联操作后的新引入数据存储在 Log Analytics 存储中，并使用 Microsoft 密钥进行加密。 只要使用有效的 Key Vault 密钥预配和配置了群集资源，就可以在解除关联前后无缝查询工作区中引入的数据。

  此资源管理器请求是异步操作。

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **响应**

  200 OK 和标头。

  进行解除关联操作后的引入数据存储在 Log Analytics 存储中，这需要 90 分钟才能完成。 可以通过两种方式检查工作区的解除关联状态：

  1. 从响应中复制 Azure-AsyncOperation URL 值，并进行[异步操作状态检查](#asynchronous-operations-and-status-check)。
  2. 发送 [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 请求并观察响应，解除关联的工作区的“功能”下方将不会具有 clusterResourceId 。

- 删除群集资源

  你需要对群集资源具有“写入”权限才能执行此操作。 如果执行的是软删除操作，则不管该删除操作是有意还是无意，均可在 14 天内恢复群集资源及其 14 天内的数据。 软删除期间，群集资源名称保持为预留，并且无法新建同名群集。 软删除结束后将释放群集资源名称，并将永久删除群集资源和数据，不可恢复 。 删除操作结束后，所有关联工作区都将与群集资源解除关联。 新引入数据存储在 Log Analytics 存储中，并使用 Microsoft 密钥进行加密。 工作区解除关联操作是异步操作，可能需要长达 90 分钟才能完成。

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常

- 恢复群集资源和数据 
  
  过去 14 天内删除的群集资源处于软删除状态，因此该群集资源及其数据均可恢复。 由于删除群集资源后所有工作区均与群集资源解除了关联，因此你需要在恢复之后重新关联工作区以实现 CMK 加密 。 当前，恢复操作由产品组手动执行。 使用 Microsoft 通道实现恢复请求。

## <a name="limitationsandconstraints"></a>限制和约束

\- CMK 在专用 Log Analytics 群集上受支持，适用于每天发送 1TB 或更多的客户。

\- 每个区域和每个订阅的群集资源的最大数目为 2 **

\- 可以将工作区关联到群集资源，然后在工作区不需要 CMK 时解除关联 ** 。 在 30 天内，工作区与特定工作区的关联数限制为 2

\- 验证是否完成 Log Analytics 群集预配之后，工作区才能与群集资源关联 **  。 完成预配之前发送到工作区的数据将被删除，并且无法恢复。

\- CMK 加密适用于 CMK 配置后的新引入数据。 CMK 配置之前引入的数据仍将使用 Microsoft 密钥进行加密。 你可以无缝查询 CMK 配置前后的数据。

\- Azure Key Vault 必须配置为可恢复。 默认情况下，这些属性不会启用，并且应使用 CLI 或 PowerShell 对其进行配置：- 必须打开 [软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- 应打开 [清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)以防软删除后强行删除机密/保险库。

- 目前不支持将群集资源移动到另一个资源组或订阅 ** 。

\- Azure Key Vault、群集资源和关联的工作区必须位于同一区域和同一 Azure Active Directory (Azure AD) 租户，但可以位于不同订阅 ** 。

\- 如果工作区已与群集资源关联，则该工作区与其他群集资源的关联将失败 **  **

## <a name="troubleshooting"></a>疑难解答

- Key Vault 可用性的行为
  - 在正常操作中，存储会缓存 AEK 一小段时间，并返回 Key Vault 定期进行解包。
    
  - 暂时性连接错误 -- 存储通过允许密钥在缓存中保留一小段时间来处理暂时性错误（超时、连接失败、DNS 问题），这可以克服可用性方面的任何小问题。 查询和引入功能将继续运行而不会中断。
    
  - 实时网站 -- 如果在约 30 分钟内未进行访问，则会导致无法使用存储帐户。 查询功能不可用，引入的数据会使用 Microsoft 密钥缓存几个小时，以避免数据丢失。 恢复对 Key Vault 的访问后，查询将变为可用，临时缓存的数据将引入到数据存储并使用 CMK 进行加密。

  - Key Vault 访问速率 -- Azure Monitor 存储为实现包装和解包操作而访问 Key Vault 的频率介于 6 到 60 秒之间。

- 如果创建群集资源并立即指定 KeyVaultProperties，则操作可能会失败，因为将系统标识分配给群集资源后才能定义访问策略 。

- 如果使用 KeyVaultProperties 更新现有的群集资源，并且 Key Vault 中缺少“Get”密钥访问策略，则该操作将失败。

- 如果尝试删除与工作区关联的群集资源，则删除操作将失败。

- 如果创建群集资源时出现冲突错误，原因可能是你在过去 14 天内删除了群集资源，并且它处于软删除期间 。 软删除期间，群集资源名称保持为预留，并且无法新建同名群集。 永久删除群集资源时，名称将在软删除期结束后释放。

- 如果在操作过程中更新群集资源，则该操作将失败。

- 如果无法部署群集资源，请验证 Azure Key Vault、群集资源和关联的 Log Analytics 工作区是否位于同一区域 ** 。 可以位于不同的订阅。

- 如果在 Key Vault 中更新密钥版本，但未更新群集资源中的新密钥标识符详细信息，则 Log Analytics 群集将继续使用之前的密钥，并且数据将变得不可访问。 更新群集资源中的新密钥标识符详细信息以恢复数据引入和数据查询功能。

- 与客户管理的密钥相关的支持和帮助，请在 Microsoft 中使用你的联系人信息。
