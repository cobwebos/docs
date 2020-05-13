---
title: Azure Monitor 客户管理的密钥
description: 使用 Azure Key Vault 密钥配置客户托管的密钥（CMK）以加密 Log Analytics 工作区中的数据的信息和步骤。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/07/2020
ms.openlocfilehash: 2838051d8e75ffbe3b7ecc9fbc655f24b57199e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198682"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor 客户管理的密钥 

本文提供了有关为 Log Analytics 工作区配置客户托管的密钥（CMK）的背景信息和步骤。 配置完成后，发送到工作区的任何数据都将用 Azure Key Vault 密钥进行加密。

建议你在配置之前查看下面的[限制和约束](#limitations-and-constraints)。

## <a name="disclaimers"></a>免责声明

- CMK 功能以专用 Log Analytics 群集的形式提供，该群集是物理群集，适用于每日或更多1TB 客户的数据存储

- CMK 定价模型目前不可用，本文未介绍它。 在日历年（CY）2020的第二季度，专用 Log Analytics 群集的定价模型将应用于任何现有的 CMK 部署。

## <a name="customer-managed-key-cmk-overview"></a>客户托管的密钥（CMK）概述

[静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是组织中常见的隐私和安全要求。 可以让 Azure 完全管理静态加密，同时可以使用各种选项来严格管理加密或加密密钥。

Azure Monitor 存储，可确保在 Azure 存储中存储时使用 Azure 托管密钥加密的所有数据。 Azure Monitor 还提供了使用你自己的密钥进行数据加密的选项，该密钥是使用系统分配的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)身份验证访问的[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)中存储的。 此密钥可以是[软件或硬件 HSM 保护的](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。
加密 Azure Monitor 使用方式与[Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)的运行方式相同。

Azure Monitor 存储访问 Key Vault 进行包装和解包操作的频率介于6到60秒之间。Azure Monitor 存储始终会在一小时内遵从关键权限更改。

在过去14天内引入的数据也保存在热缓存（SSD 支持的）中，以实现高效的查询引擎操作。 此数据将使用 Microsoft 密钥进行加密，而不考虑 CMK 配置，但对 SSD 数据的控制将遵循[密钥吊销](#cmk-kek-revocation)并无法访问。 我们正在努力在2020的第二部分中使用 CMK 加密 SSD 数据。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure Monitor 中的工作原理

Azure Monitor 利用系统分配的托管标识授予对 Azure Key Vault 的访问权限。系统分配的托管标识只能与单个 Azure 资源关联。 群集级别支持专用 Log Analytics 群集的标识，这规定 CMK 功能在专用 Log Analytics 群集上提供。 为了支持多个工作区上的 CMK，新的 Log Analytics*群集*资源作为 Key Vault 和 Log Analytics 工作区之间的中间标识连接执行。 此概念维护专用 Log Analytics 群集与 Log Analytics*群集*资源之间的标识，而相关工作区的数据则受 Key Vault 密钥保护。 专用 Log Analytics 群集存储使用 \' 与*群集*资源关联的托管标识通过 Azure Active Directory 进行身份验证和访问 Azure Key Vault。

![CMK 概述](media/customer-managed-keys/cmk-overview-8bit.png)
1.    客户的 Key Vault。
2.    客户的 Log Analytics*群集*资源具有 Key Vault 权限的托管标识–在专用 Log Analytics 群集级别支持标识。
3.    专用 Log Analytics 群集。
4.    与 CMK 加密的*群集*资源关联的客户工作区。

## <a name="encryption-keys-operation"></a>加密密钥操作

存储数据加密涉及3种类型的密钥：

- **KEK**密钥加密密钥（CMK）
- **AEK** -帐户加密密钥
- **DEK** -数据加密密钥

下列规则适用：

- 专用 Log Analytics 群集存储帐户为每个存储帐户生成唯一的加密密钥，这称为 AEK。

- AEK 用于派生 Dek，后者是用于对写入磁盘的每个数据块进行加密的密钥。

- 在 Key Vault 中配置密钥并在*群集*资源中引用密钥时，Azure 存储会将请求发送到 Azure Key Vault，以包装和解包 AEK 以执行数据加密和解密操作。

- 你的 KEK 绝不会离开你的 Key Vault，在使用 HSM 密钥的情况下，它绝不会离开硬件。

- Azure 存储使用与*群集*资源关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 进行身份验证和访问。

## <a name="cmk-provisioning-procedure"></a>CMK 预配过程

1. 订阅允许列表-若要确保在你的区域中拥有专用 Log Analytics 群集所需的容量，我们需要事先验证和加入你的订阅
2. 创建 Azure Key Vault 和存储密钥
3. 创建*群集*资源
5. 授予 Key Vault 的权限
6. 关联 Log Analytics 工作区

当前 UI 中不支持此过程，并且通过 REST API 执行设置过程。

> [!IMPORTANT]
> 任何 API 请求都必须在请求标头中包含持有者授权令牌。

例如：

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

其中， *eyJ0eXAiO*表示完整的授权令牌。 

可以使用以下方法之一获取该令牌：

1. 使用[应用注册](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。
2. 在 Azure 门户中
    1. 在 "开发人员工具" （F12）中导航到 Azure 门户
    1. 在 "batch？ api 版本" 实例之一中查找 "请求标头" 下的授权字符串。 如下所示： "authorization：持有者 eyJ0eXAiO ..."。 
    1. 按照以下示例，将其复制并添加到 API 调用。
3. 导航到 Azure REST 文档站点。 在任何 API 上按 "重试"，并复制持有者令牌。

### <a name="asynchronous-operations-and-status-check"></a>异步操作和状态检查

此配置过程中的某些操作是异步运行的，因为它们无法快速完成。 异步操作的响应最初在接受时返回 HTTP 状态代码200（OK）和标头*AsyncOperation*属性：
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

可以通过将 GET 请求发送到*AsyncOperation*标头值来检查异步操作的状态：
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

响应包含有关操作及其*状态*的信息。 它可以是以下项之一：

操作正在进行中
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

操作已完成
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

### <a name="subscription-whitelisting"></a>订阅允许列表

CMK 功能是一项早期的访问功能。 你计划创建*群集*资源的订阅必须预先列入 Azure 产品组。 使用你的联系人加入 Microsoft 提供订阅 Id。

> [!IMPORTANT]
> CMK 功能为地区性。 Azure Key Vault、*群集*资源和关联 Log Analytics 工作区必须位于同一区域中，但它们可以位于不同的订阅中。

### <a name="storing-encryption-key-kek"></a>存储加密密钥（KEK）

创建或使用已有的 Azure Key Vault，或导入用于数据加密的密钥。 必须将 Azure Key Vault 配置为可恢复，以保护你的密钥和 Azure Monitor 中对你的数据的访问。 你可以在 Key Vault 中的 "属性" 下验证此配置，应同时启用*软删除*和*清除保护*。

![软删除和清除保护设置](media/customer-managed-keys/soft-purge-protection.png)

可以通过 CLI 和 PowerShell 使用这些设置：
- [软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)会防止在软删除后强行删除密钥/保管库

### <a name="create-cluster-resource"></a>创建*群集*资源

此资源用作 Key Vault 与 Log Analytics 工作区之间的中间标识连接。 收到订阅处于白名单状态的确认后，请在工作区所在的区域创建 Log Analytics*群集*资源。

创建*群集*资源时，必须指定*容量预留*级别（sku）。 *容量预留*级别可以在1000到 2000 GB （每天）范围内，你可以在稍后100的步骤中对其进行更新。 如果需要的容量预留级别超过 2000 GB，请联系我们 LAIngestionRate@microsoft.com 。 [了解详细信息](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)
    
*BillingType*属性确定*群集*资源及其数据的计费归属：
- *群集*（默认值）--计费属于托管*群集*资源的订阅
- *工作区*-计费按比例托管工作区的订阅 

> [!NOTE]
> 创建*群集*资源后，可以使用 "keyVaultProperties" 或 "修补程序 REST 请求" 将其更新为*sku*、 *keyVaultProperties*或*billingType* 。

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
在创建时将标识分配给*群集*资源。

**响应**

200正常和标头。

在完成预配专用 Log Analytics 群集一段时间后，你可以通过两种方式检查预配状态：

1. 从响应中复制 "AsyncOperation URL" 值，并遵循[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 向*群集*资源发送 GET 请求，并查看*provisioningState*值。 它在预配时*ProvisioningAccount* ，并在完成后*成功*完成。


```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 复制并保存响应，因为你将在后续步骤中需要详细信息。

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

"PrincipalId" GUID 由*群集*资源的托管标识服务生成。

### <a name="grant-key-vault-permissions"></a>授予 Key Vault 权限

使用授予*群集*资源权限的新访问策略更新 Key Vault。 是 Azure Monitor 存储使用这些权限进行数据加密。 在 Azure 门户中打开你的 Key Vault，然后单击 "访问策略"，然后单击 "+ 添加访问策略"，使用以下设置创建策略：

- 密钥权限：选择 "获取"、"环绕键" 和 "解包密钥" 权限。
- 选择主体：输入在上一步的响应中返回的主体 id 值。

![授予 Key Vault 权限](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

需要使用*Get*权限来验证 Key Vault 是否已配置为可恢复，以保护你的密钥和对你的 Azure Monitor 数据的访问。

### <a name="update-cluster-resource-with-key-identifier-details"></a>更新具有密钥标识符详细信息的群集资源

此步骤在初始期间以及在以后的密钥版本更新中执行 Key Vault。 它通知 Azure Monitor 有关用于数据加密的密钥版本的存储。 更新后，将使用新密钥来包装并解包到存储密钥（AEK）。

若要用 Key Vault*密钥标识符*详细信息更新*群集*资源，请在 Azure Key Vault 中选择密钥的当前版本，以获取密钥标识符详细信息。

![授予 Key Vault 权限](media/customer-managed-keys/key-identifier-8bit.png)

更新具有密钥标识符详细信息的*群集*资源 KeyVaultProperties。

**更新**

此资源管理器请求是在更新密钥标识符详细信息时异步操作，而在更新容量值时，它是同步的。

> [!Note]
> 可以在*群集*资源中提供部分正文来更新*sku*、 *keyVaultProperties*或*billingType*。

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
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       }
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" 包含 Key Vault 密钥标识符详细信息。

**响应**

200正常和标头。
它会将密钥标识符传播到几分钟才能完成。 可以通过两种方式检查更新状态：
1. 从响应中复制 "AsyncOperation URL" 值，并遵循[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 向*群集*资源发送 GET 请求，并查看*KeyVaultProperties*属性。 你最近更新的密钥标识符详细信息应在响应中返回。

密钥标识符更新完成后，对*群集*资源的 GET 请求的响应应如下所示：

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
      keyVaultUri: "https://key-vault-name.vault.azure.net",
      kyName: "key-name",
      keyVersion: "current-version"
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

### <a name="workspace-association-to-cluster-resource"></a>与*群集*资源的工作区关联

需要对工作区和*群集*资源具有 "写入" 权限才能执行此操作，包括以下操作：

- 在工作区中： Microsoft.operationalinsights/工作区/写入
- 在*群集*资源中： microsoft.operationalinsights/群集/写入

> [!IMPORTANT]
> 只有在完成专用 Log Analytics 群集预配后，才应执行此步骤。 如果在预配前关联工作区和引入数据，引入数据将被删除，并且将无法恢复。

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

200正常和标头。

关联操作后，引入数据将与托管密钥一起加密存储，这可能需要长达90分钟的时间才能完成。 可以通过两种方式检查工作区关联状态：

1. 从响应中复制 "AsyncOperation URL" 值，并遵循[异步操作状态检查](#asynchronous-operations-and-status-check)。
2. 发送[工作区– Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)请求并观察响应，关联的工作区在 "功能" 下将具有 clusterResourceId。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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
    "retentionInDays": days,
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

## <a name="cmk-kek-revocation"></a>CMK （KEK）吊销

可以通过禁用密钥，或在 Key Vault 中删除*群集*资源访问策略，来撤销对数据的访问权限。 专用 Log Analytics 群集存储在一小时或更短时间内将始终遵循关键权限的更改，并且存储将变为不可用。 与*群集*资源关联的工作区的任何数据引入会被删除，并且查询将失败。 以前引入的数据在存储中保持不可访问，因为*群集*资源和工作区不会被删除。 不可访问的数据由数据保留策略控制，当达到保留时，将被清除。 

在过去14天内引入的数据也保存在热缓存（SSD 支持的）中，以实现高效的查询引擎操作。 这会在密钥吊销操作中被删除，也会变得不可访问。

存储会定期轮询你的 Key Vault，尝试解包加密密钥，一旦访问，数据引入和查询将在30分钟内恢复。

## <a name="cmk-kek-rotation"></a>CMK （KEK）旋转

CMK 的旋转需要在 Azure Key Vault 中用新的密钥版本显式更新*群集*资源。 按照 "通过密钥标识符详细信息更新*群集*资源" 步骤中的说明进行操作。 如果未更新*群集*资源中的新密钥标识符详细信息，专用 Log Analytics 群集存储将继续使用以前的密钥。

你的所有数据在循环之前和之后都保持可访问的密钥旋转操作（包括数据引入），因为数据始终用帐户加密密钥（AEK）加密，而 AEK 现在是用新的密钥加密密钥（KEK）版本在 Key Vault 中进行加密。

## <a name="limitations-and-constraints"></a>限制和约束

- 对于每日发送1TB 或更多的客户，专用 Log Analytics 群集上支持 CMK。

- 每个区域和订阅的*群集*资源的最大数目为2

- 可以将工作区关联到*群集*资源，然后在不再需要 CMK 的数据或任何其他原因时取消关联。 在30天内，你可以在工作区中执行的工作区关联数限制为2

- 仅在验证专用 Log Analytics 群集预配完成后，才应执行与*群集*资源的工作区关联。 在完成之前发送到你的工作区的数据将被删除，并且将无法恢复。

- CMK 加密适用于 CMK 配置后的新引入数据。 在 CMK 配置之前引入的数据仍将通过 Microsoft 密钥进行加密。 你可以无缝地查询 CMK 配置前后的数据引入。

- 必须将 Azure Key Vault 配置为可恢复。 默认情况下，这些属性不会启用，应该使用 CLI 或 PowerShell 进行配置：

  - 必须打开[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - 即使在软删除后，也应打开[清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防强制删除密钥/保管库。

- 当前不支持*群集*资源移动到另一个资源组或订阅。

- Azure Key Vault、*群集*资源和相关联的工作区必须位于同一区域和同一 Azure Active Directory （Azure AD）租户中，但它们可以位于不同的订阅中。

- 如果与*群集*资源关联的工作区与其他*群集*资源关联，则工作区关联将失败


## <a name="management"></a>管理

- **获取资源组的所有*群集*资源**

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
              keyVaultUri: "https://key-vault-name.vault.azure.net",
              keyName: "key-name",
              keyVersion: "current-version"
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

- **获取订阅的所有*群集*资源**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **响应**
    
  与 "资源组的*群集*资源" 相同的响应，但在订阅范围内。

- **更新*群集*资源中的*容量预留***

  当与关联的工作区的数据量发生变化时，你想要适当地更新产能预留级别。 按照[更新*群集*资源](#update-cluster-resource-with-key-identifier-details)进行操作并提供新的容量价值。 它可以在1000到 2000 GB 的范围内，在100的范围内。 如果每日级别高于 2000 GB，请联系你的 Microsoft 联系人以启用它。 请注意，你无需提供完整的 REST 请求正文并应包含 sku：

  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ``` 

- **更新*群集*资源中的*billingType***

  *BillingType*属性确定*群集*资源及其数据的计费归属：
  - *群集*（默认值）--计费属于托管群集资源的订阅
  - *工作区*-计费按比例托管工作区的订阅
  
  按照[更新*群集*资源](#update-cluster-resource-with-key-identifier-details)，提供新的 billingType 值。 请注意，不需要提供完整的 REST 请求正文，应包括*billingType*：

  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **取消关联工作区**

  需要对工作区和*群集*资源具有 "写入" 权限才能执行此操作。 你可以随时取消关联你的*群集*资源的工作区。 解除关联操作后的新引入数据存储在 Log Analytics 存储中，并通过 Microsoft 密钥进行加密。 只要预配并*配置了有效*Key Vault 密钥，就可以在取消关联之前和之后查询已引入到工作区的数据。

  此资源管理器请求是异步操作。

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  ```

  **响应**

  200正常和标头。

  引入数据存储在 Log Analytics 存储中后，此操作可能需要90分钟才能完成。 可以通过两种方式检查工作区的取消关联状态：

  1. 从响应中复制 "AsyncOperation URL" 值，并遵循[异步操作状态检查](#asynchronous-operations-and-status-check)。
  2. 发送[工作区– Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)请求并观察响应，解除关联的工作区在*功能*下将不具有*clusterResourceId* 。


- **删除*群集*资源**

  需要对*群集*资源具有 "写入" 权限才能执行此操作。 执行软删除操作可以在14天内恢复*群集*资源（包括其数据），无论删除是意外的还是有意的。 *群集*资源名称在软删除期间仍然保留，因此无法使用该名称创建新群集。 软删除期结束后，*群集**资源名称*将被永久删除，并且不可恢复。 删除操作时，任何关联的工作区都将从*群集*资源中解除关联。 新的引入数据存储在 Log Analytics 存储中，并通过 Microsoft 密钥进行加密。 工作区解除关联的操作是异步的，最长可能需要90分钟才能完成。

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常

- **恢复*群集*资源和数据** 
  
  在过去14天内删除的*群集*资源处于软删除状态，可以使用其数据进行恢复。 由于在删除时所有工作区都与*群集*资源解除关联，因此需要在恢复 CMK 加密后重新关联工作区。 当前，该产品组手动执行恢复操作。 使用你的 Microsoft 渠道进行恢复请求。

## <a name="troubleshooting"></a>疑难解答
- Key Vault 可用性的行为
  - 在正常操作中，存储缓存 AEK 一小段时间，并返回到 Key Vault 进行定期解包。
    
  - 暂时性连接错误-通过允许密钥在缓存中保持较短的时间，并在可用性上克服任何小信号，存储处理暂时性错误（超时、连接失败、DNS 问题）。 查询和引入功能在不中断的情况下继续进行。
    
  - 实时网站--在30分钟内无法使用存储帐户。 查询功能不可用，引入数据使用 Microsoft 密钥缓存了几个小时，以避免数据丢失。 当还原对 Key Vault 的访问时，查询将变为可用，临时缓存数据将引入到数据存储区，并使用 CMK 进行加密。

- 如果创建*群集*资源并立即指定 KeyVaultProperties，则操作可能会失败，因为在向*群集*资源分配系统标识之前无法定义访问策略。

- 如果使用 KeyVaultProperties 更新现有*群集*资源但 Key Vault 中缺少 "Get" 密钥访问策略，则操作将失败。

- 如果尝试删除与工作区关联的*群集*资源，则删除操作将失败。

- 如果在创建*群集*资源时出现冲突错误，可能是您在过去14天内删除了*群集*资源，而且它在软删除期间。 *群集*资源名称在软删除期间仍然保留，因此无法使用该名称创建新群集。 删除*群集*资源后，该名称将在软删除期间释放。

- 如果在操作过程中更新*群集*资源，操作将失败。

- 如果无法部署*群集*资源，请验证 Azure Key Vault、 *群集*   资源和关联 Log Analytics 工作区是否位于同一区域。 可以位于不同的订阅中。

- 如果你 Key Vault 更新密钥版本，但未更新*群集*资源中的新密钥标识符详细信息，Log Analytics 群集将继续使用以前的密钥，你的数据将变得不可访问。 更新*群集*资源中的新密钥标识符详细信息，恢复数据引入和查询数据。

- 有关与客户托管密钥相关的支持和帮助，请使用你的联系人加入 Microsoft。

