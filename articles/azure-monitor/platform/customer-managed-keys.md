---
title: 客户管理的密钥配置 Azure Monitor
description: 使用 Azure Key Vault 密钥配置客户托管的密钥（CMK）以加密 Log Analytics 工作区中的数据的信息和步骤。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/24/2020
ms.openlocfilehash: 6a999df6daf2fde5133143fe9b22a65d628ecfb2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663941"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>客户管理的密钥配置 Azure Monitor 

本文提供了背景信息和步骤，用于为 Log Analytics 工作区和 Application Insights 组件配置客户托管的密钥（CMK）。 配置后，发送到你的工作区或组件的任何数据都将用 Azure Key Vault 密钥进行加密。

建议你在配置之前查看下面的[限制和约束](#limitations-and-constraints)。

## <a name="disclaimers"></a>免责声明

- Azure Monitor CMK 是一项早期访问功能，并且已对已注册的订阅启用。

- 本文中所述的 CMK 部署以生产质量形式提供，并受支持，尽管这是一项早期的访问功能。

- CMK 功能是在专用的数据存储群集上传递的，它是一个 Azure 数据资源管理器（ADX）群集，适用于每天发送1TB 的客户。 

- CMK 定价模型目前不可用，本文未介绍它。 在日历年（CY）2020的第二季度，专用 ADX 群集的定价模型将应用于任何现有的 CMK 部署。

- 本文介绍 Log Analytics 工作区的 CMK 配置。 本文章还支持 CMK for Application Insights 组件，但附录中列出了不同之处。

> [!NOTE]
> Log Analytics 和 Application Insights 使用相同的数据存储平台和查询引擎。
> 我们通过将 Application Insights 集成到 Log Analytics 中将这两个存储组合在一起，以便在 Azure Monitor 下创建单个统一的日志存储。 此更改计划于第二季度的 calendar 2020 年。 如果你不需要为 Application Insights 数据部署 CMK，则建议等待合并完成，因为合并将中断此类部署，并且你需要在迁移到日志后重新配置 CMK分析工作区。 每日1TB 最小值适用于群集级别，直到第二季度合并完成，Application Insights 和 Log Analytics 需要单独的群集。

## <a name="customer-managed-key-cmk-overview"></a>客户托管的密钥（CMK）概述

[静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是组织中常见的隐私和安全要求。 可以让 Azure 完全管理静态加密，同时可以使用各种选项来严格管理加密或加密密钥。

Azure Monitor 的数据存储区确保在 Azure 存储中存储时使用 Azure 托管密钥加密的所有数据。 Azure Monitor 还提供了使用自己的密钥进行数据加密的选项，该密钥是使用系统分配的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)身份验证访问的[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)中存储的。 此密钥可以是[软件或硬件 HSM 保护的](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。
加密 Azure Monitor 使用方式与[Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)的运行方式相同。

Azure Monitor 存储访问 Key Vault 进行包装和解包操作的频率介于6到60秒之间。 Azure Monitor 存储始终会在一小时内遵从关键权限更改。

在过去14天内引入的数据也保存在热缓存（SSD 支持的）中，以实现高效的查询引擎操作。 无论 CMK 配置如何，都将使用 Microsoft 密钥对此数据进行加密，但我们正努力在2020初期使用 CMK 对其进行加密。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure Monitor 中的工作原理

Azure Monitor 利用系统分配的托管标识授予对 Azure Key Vault 的访问权限。 系统分配的托管标识只能与单个 Azure 资源关联。 群集级别支持 Azure Monitor 数据存储（ADX 群集）的标识，这表示 CMK 功能在专用 ADX 群集上提供。 为了支持多个工作区上的 CMK，新的 Log Analytics 资源（*群集*）作为 Key Vault 和 Log Analytics 工作区之间的中间标识连接执行。 此概念符合系统分配的标识约束，并在 ADX 群集与 Log Analytics*群集*资源之间维护标识，而所有关联的工作区的数据都受 Key Vault 密钥保护。 是 ADX 群集存储使用与*群集*资源关联的托管\'标识，通过 Azure Active Directory 进行身份验证和访问 Azure Key Vault。

![CMK 概述](media/customer-managed-keys/cmk-overview.png)
1.  客户的 Key Vault。
2.  客户的 Log Analytics 群集资源具有 Key Vault 权限的托管标识–在数据存储（ADX 群集）级别支持标识。
3.  Azure Monitor 专用 ADX 群集。
4.  与 CMK 加密的群集资源关联的客户工作区。

## <a name="encryption-keys-management"></a>加密密钥管理

存储数据加密涉及3种类型的密钥：

- **KEK** Key Vault 中的密钥加密密钥（CMK）
- **AEK** -帐户加密密钥
- **DEK** -数据加密密钥

下列规则适用：

- ADX 存储帐户为每个存储帐户生成唯一的加密密钥，该密钥称为 AEK。

- AEK 用于派生 Dek，后者是用于对写入磁盘的每个数据块进行加密的密钥。

- 在 Key Vault 中配置密钥并在*群集*资源中引用密钥时，Azure 存储会将 AEK 与 KEK 一起包装在 Azure Key Vault 中。

- 你的 KEK 绝不会离开你的 Key Vault，在使用 HSM 密钥的情况下，它绝不会离开硬件。

- Azure 存储使用与*群集*资源关联的托管标识，通过 Azure Active Directory 对 Azure Key Vault 进行身份验证和访问。

- 对于读/写操作，Azure 存储会将请求发送到 Azure Key Vault 以包装和解包 AEK，以执行加密和解密操作。

## <a name="cmk-provisioning-procedure"></a>CMK 预配过程

对于 Application Insights CMK 配置，请按照步骤3和6的附录内容进行操作。

1. 订阅允许列表--这是早期访问功能所必需的
2. 创建 Azure Key Vault 和存储密钥
3. 创建*群集*资源
4. Azure Monitor 数据存储（ADX 群集）预配
5. 授予 Key Vault 的权限
6. Log Analytics 工作区关联

当前 UI 中不支持此过程，并且通过 REST API 执行设置过程。

> [!IMPORTANT]
> 任何 API 请求都必须在请求标头中包含持有者授权令牌。

例如：

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

其中， *eyJ0eXAiO*表示完整的授权令牌。 

可以使用以下方法之一获取该令牌：

1. 使用[应用注册](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。

2. 在 Azure 门户中
    1. 在 "开发人员工具（F12）" 中导航到 Azure 门户
    1. 在 "batch？ api 版本" 实例之一中查找 "请求标头" 下的授权字符串。 如下所示： "authorization：载荷 \<token\>"。 
    1. 按照以下示例，将其复制并添加到 API 调用。

3. 导航到 Azure REST 文档站点。 在任何 API 上按 "重试"，并复制持有者令牌。

### <a name="subscription-whitelisting"></a>订阅允许列表

CMK 功能是一项早期的访问功能。 你计划创建*群集*资源的订阅必须预先列入 Azure 产品组。 使用你的联系人加入 Microsoft 提供订阅 Id。

> [!IMPORTANT]
> CMK 功能为地区性。 Azure Key Vault、存储帐户、*群集*资源和关联 Log Analytics 工作区必须位于同一区域中，但它们可以位于不同的订阅中。

### <a name="storing-encryption-key-kek"></a>存储加密密钥（KEK）

创建 Azure Key Vault 资源，然后生成或导入用于数据加密的密钥。

必须将 Azure Key Vault 配置为可恢复，以保护你的密钥和对你的 Azure Monitor 数据的访问。

可以通过 CLI 和 PowerShell 使用这些设置：
- 必须打开[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- 应打开[清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止即使在软删除后强行删除密钥/保管库

### <a name="create-cluster-resource"></a>创建*群集*资源

此资源用作 Key Vault 与工作区之间的中间标识连接。 收到订阅处于白名单状态的确认后，请在工作区所在的区域创建 Log Analytics*群集*资源。 Application Insights 和 Log Analytics 需要单独的群集资源。 *群集*资源的类型在创建时通过将 "clusterType" 属性设置为 "LogAnalytics" 或 "applicationinsights.config" 来定义。 不能更改群集资源类型。

对于 Application Insights CMK 配置，请遵循此步骤的附录内容。

**创建**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
在创建时将标识分配给*群集*资源。
对于 Application Insights CMK，"clusterType" 值为 "Applicationinsights.config"。

**响应**

202已接受。 这是一个用于异步操作的标准资源管理器响应。

如果出于任何原因删除了*群集*资源（例如，使用不同的名称或 clusterType 创建该资源），请使用以下 REST API：

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure Monitor 数据存储（ADX 群集）预配

在此功能的早期访问期内，在完成前面的步骤后，产品团队将手动设置 ADX 群集。 使用你的 Microsoft 渠道提供*群集*资源详细信息。 复制*群集*资源 GET REST API 的 JSON 响应：

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**响应**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-Id"
    },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

"principalId" 是由托管标识服务为*群集*资源生成的 GUID。

> [!IMPORTANT]
> 复制并保留 "cluster id" 值，因为在后续步骤中需要用到它。


### <a name="grant-key-vault-permissions"></a>授予 Key Vault 权限

> [!IMPORTANT]
> 此步骤应在你通过 Microsoft 通道从产品组收到确认 Azure Monitor 数据存储（ADX 群集）预配完成后执行。 在此设置之前更新 Key Vault 访问策略可能会失败。

使用授予*群集*资源权限的新访问策略更新 Key Vault。 Underlaying Azure Monitor 存储使用这些权限进行数据加密。
在 Azure 门户中打开你的 Key Vault，然后单击 "访问策略"，然后单击 "+ 添加访问策略"，使用以下设置创建新策略：

- 密钥权限：选择 "获取"、"环绕键" 和 "解包密钥" 权限。
- 选择主体：输入在上一步的响应中返回的群集 id 值。

![授予 Key Vault 权限](media/customer-managed-keys/grant-key-vault-permissions.png)

需要使用*Get*权限来验证 Key Vault 是否已配置为可恢复，以保护你的密钥和对你的 Azure Monitor 数据的访问。

### <a name="update-cluster-resource-with-key-identifier-details"></a>更新具有密钥标识符详细信息的群集资源

此步骤适用于你的 Key Vault 中的后续密钥版本更新。 使用 Key Vault*密钥标识符*详细信息更新*群集*资源，以允许 Azure Monitor 存储使用新的密钥版本。 在 Azure Key Vault 中选择密钥的当前版本，以获取密钥标识符详细信息。

![授予 Key Vault 权限](media/customer-managed-keys/key-identifier-8bit.png)

更新具有密钥标识符详细信息的*群集*资源 KeyVaultProperties。

**Update**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>",
   "identity": { 
     "type": "systemAssigned" 
     }
}
```
"KeyVaultProperties" 包含 Key Vault 密钥标识符详细信息。

**响应**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
       "KeyVaultProperties": {
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>与*群集*资源的工作区关联

> [!NOTE]
> 仅在通过 Microsoft 通道从产品组收到确认**Azure Monitor 的数据存储（ADX 群集）预配**的情况下，**才**应执行此步骤。 如果在此**设置**之前将工作区和引入数据关联起来，则数据将被删除，并且将无法恢复。

对于 Application Insights CMK 配置，请遵循此步骤的附录内容。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
*ClusterDefinitionId*是在上一步骤的响应中提供的*clusterId*值。

**响应**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

关联后，发送到你的工作区的数据将存储在你的托管密钥中进行加密。

### <a name="workspace-association-verification"></a>工作区关联验证
可以通过查看工作区[–获取](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)响应来验证是否已将工作区关联到*群集*资源。 关联的工作区将有一个具有*群集*资源 ID 的 "clusterResourceId" 属性。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
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
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
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

Azure Monitor 存储始终会在一小时内对关键权限进行更改，通常更快，存储将不可用。 与*群集*资源关联的工作区的任何数据引入将被删除，并且查询将失败。 只要你的密钥已被撤销，并且你的工作区不会被删除，以前引入的数据在 Azure Monitor 存储中仍将无法访问。 不可访问的数据由数据保留策略控制，当达到保留时，将被清除。

存储会定期轮询 Key Vault 来尝试解包加密密钥，一旦访问，数据引入和查询将在30分钟内恢复。

## <a name="cmk-kek-rotation"></a>CMK （KEK）旋转

旋转 CMK 需要用新的 Azure Key Vault 密钥版本显式更新*群集*资源。 若要用新的密钥版本更新 Azure Monitor，请按照 "用*密钥标识符*详细信息更新*群集*资源" 步骤中的说明进行操作。

如果在 Key Vault 中更新密钥，且不更新*群集*资源 * 中的新*密钥标识符*详细信息，Azure Monitor 存储将继续使用以前的密钥。

## <a name="limitations-and-constraints"></a>限制和约束

- ADX 群集级别支持 CMK 功能，需要专用的 Azure Monitor ADX 群集

- 每个订阅的最大*群集*资源数限制为5

- 只有在从 ADX 群集预配的产品组收到确认后，才应执行与工作区的*群集*资源关联。 此设置之前发送的数据将被删除，并且将无法恢复。

- CMK 加密适用于 CMK 配置后的新引入数据。 在 CMK 配置之前引入的数据，并通过 Microsoft 密钥进行加密。 你可以无缝地在配置前后查询数据。

- 一旦将工作区与*群集*资源关联，就不能将其与*群集*资源解除关联，因为使用密钥对数据进行了加密，并且在 Azure Key Vault 中没有 KEK 可访问。

- 必须将 Azure Key Vault 配置为可恢复。 默认情况下，这些属性不会启用，应该使用 CLI 和 PowerShell 进行配置：

  - 必须打开[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - 应打开[清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止即使在软删除后强行删除密钥/保管库

- Application Insights 和 Log Analytics 需要单独的*群集*资源。 *群集*资源的类型在创建时通过将 "clusterType" 属性设置为 "LogAnalytics" 或 "applicationinsights.config" 来定义。 不能更改*群集*资源类型。

- 当前不支持*群集*资源移动到另一个资源组或订阅。

- Azure Key Vault、*群集*资源和相关联的工作区必须位于同一区域和同一 Azure Active Directory （Azure AD）租户中，但它们可以位于不同的订阅中。

- 如果与*群集*资源关联的工作区与其他*群集*资源关联，则工作区关联将失败

## <a name="troubleshooting-and-management"></a>故障排除和管理

- Key Vault 可用性
    - 在正常操作中，存储将 AEK 一小段时间定期返回到 Key Vault 以解包。
    
    - 暂时性连接错误。 存储通过允许密钥在缓存中保持较短的时间来处理暂时性错误（超时、连接失败、DNS 问题），这会使任何小的信号。 查询和引入功能在不中断的情况下继续进行。
    
    - 实时站点，将无法使用约30分钟的时间。 查询功能不可用，引入数据使用 Microsoft 密钥缓存了几个小时，以避免数据丢失。 当还原对 Key Vault 的访问时，查询将变为可用，临时缓存数据将引入到数据存储区，并使用 CMK 进行加密。

- 如果创建*群集*资源并立即指定 KeyVaultProperties，则操作可能会失败，因为在向*群集*资源分配系统标识之前无法定义访问策略。

- 如果使用 KeyVaultProperties 更新现有*群集*资源但 Key Vault 中缺少 "Get" 密钥访问策略，则操作将失败。

- 如果尝试删除与工作区关联的*群集*资源，则删除操作将失败。

- 使用此 API 获取资源组的所有*群集*资源：

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- 使用此 API 调用获取订阅的所有*群集*资源：

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **响应**
    
  与 "资源组的*群集*资源" 相同的响应，但在订阅范围内。
    
- 使用此 API 调用删除*群集*资源--需要先删除所有关联的工作区，然后才能删除*群集*资源：

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常


## <a name="appendix"></a>附录

同时还支持 Application Insights 客户托管密钥（CMK），但你应该考虑以下更改，以帮助你规划应用程序见解组件的 CMK 部署。

Log Analytics 和 Application Insights 使用相同的数据存储平台和查询引擎。 我们将这两个存储结合 Application Insights 集成到 Log Analytics 中，在第二季度 Azure Monitor 下提供单个统一的日志存储
2020. 此更改会使你的应用程序深入了解 Log Analytics 工作区，并在你的工作区上配置 CMK 时进行查询、见解和其他改进，同时还适用于你的 Application Insights 数据。

> [!NOTE]
> 如果在集成之前不需要为应用程序见解数据部署 CMK，建议等待使用 Application Insights CMK，因为此类部署将通过集成中断，并且你必须在迁移到日志后重新配置 CMK。分析工作区。 每日1TB 最小值适用于群集级别，直到第二季度合并完成，Application Insights 和 Log Analytics 需要单独的群集。

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK 配置

Application Insights CMK 的配置与本文中所述的过程完全相同，包括除以下步骤以外的约束和故障排除：

- 创建*群集*资源

- 将组件关联到*群集*资源

为 Application Insights 配置 CMK 时，请使用这些步骤，而不是上面列出的步骤。

### <a name="create-a-cluster-resource"></a>创建*群集*资源

此资源用作 Key Vault 与组件之间的中间标识连接。 收到订阅处于白名单状态的确认后，请在组件所在的区域创建 Log Analytics*群集*资源。 *群集*资源的类型在创建时通过将*clusterType*属性设置为*LogAnalytics*或*applicationinsights.config*定义。 它应为 Application Insights CMK 的*applicationinsights.config* 。 配置后无法更改*clusterType*设置。

**创建**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**响应**

在创建时将标识分配给*群集*资源。

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"principalId" 是由托管标识服务生成的 GUID。

> [!IMPORTANT]
> 复制并保留 "cluster id" 值，因为在后续步骤中需要用到它。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>使用[组件-创建或更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)API 将组件关联到*群集*资源

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" 是在上一步骤的响应中提供的 "clusterId" 值。
"kind" 示例为 "web"。

**响应**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" 是与此组件关联的*群集*资源 ID。

关联后，发送到你的组件的数据将存储在你的托管密钥中进行加密。
