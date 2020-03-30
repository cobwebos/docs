---
title: Azure 监视器客户管理的关键配置
description: 配置客户管理密钥 （CMK） 以使用 Azure 密钥保管库密钥加密日志分析工作区中的数据的信息和步骤。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 03/26/2020
ms.openlocfilehash: 563a50d4589a83f710caa8ff2d2d95065909fc5f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384171"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure 监视器客户管理的关键配置 

本文提供了后台信息和步骤，用于为日志分析工作区和应用程序见解组件配置客户管理密钥 （CMK）。 配置完成后，发送到工作区或组件的任何数据都使用 Azure 密钥保管库密钥进行加密。

我们建议您在配置之前查看下面的[限制和约束](#limitations-and-constraints)。

## <a name="disclaimers"></a>免责声明

- Azure 监视器 CMK 是一种早期访问功能，已为注册订阅启用。

- 本文中描述的 CMK 部署以生产质量提供，并且支持此类部署，尽管它是早期访问功能。

- CMK 功能在专用数据存储群集上提供，该群集是 Azure 数据资源管理器 （ADX） 群集，适用于每天发送 1TB 或更多的客户。 

- CMK 定价模型当前不可用，本文未介绍此版本。 专用 ADX 群集的定价模型预计将在 2020 日历年 （CY） 2020 年第二季度使用，并将应用于任何现有的 CMK 部署。

- 本文介绍了日志分析工作区的 CMK 配置。 在附录中列出了差异时，还支持使用本文支持 CMK 应用程序见解组件。

> [!NOTE]
> 日志分析和应用程序见解使用相同的数据存储平台和查询引擎。
> 我们通过将应用程序见解集成到日志分析中来将这两个存储整合在一起，以创建 Azure 监视器下的单一统一日志存储。 此更改计划于 2020 日历年第二季度进行。 如果到那时不必为应用程序见解数据部署 CMK，我们建议您等待合并的完成，因为此类部署将因合并而中断，并且在迁移到 Log 后必须重新配置 CMK分析工作区。 每天 1TB 的最小值适用于群集级别，直到第二季度合并完成，应用程序见解和日志分析需要单独的群集。

## <a name="customer-managed-key-cmk-overview"></a>客户管理密钥 （CMK） 概述

[静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)是组织中常见的隐私和安全要求。 您可以让 Azure 完全管理静态加密，同时您可以有各种选项来密切管理加密或加密密钥。

Azure 监视器数据存储可确保在 Azure 存储中存储时使用 Azure 管理的密钥对静态加密的所有数据。 Azure 监视器还提供一个选项，用于使用存储在[Azure 密钥保管库中](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的密钥进行数据加密，该密钥使用系统分配的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)身份验证进行访问。 此密钥可以是[软件或硬件-HSM保护](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。
Azure 监视器使用加密与[Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)操作方式相同。

Azure 监视器存储访问密钥保管库进行换行和解包操作的频率为 6 到 60 秒。Azure 监视器存储始终在一小时内尊重密钥权限的更改。

过去 14 天内的引入数据也保存在热缓存（SSD 支持）中，以便高效运行查询引擎。 无论 CMK 配置如何，此数据仍使用 Microsoft 密钥进行加密，但我们正在努力在 2020 年初使用 CMK 加密 SSD。

## <a name="how-cmk-works-in-azure-monitor"></a>CMK 在 Azure 监视器中的工作方式

Azure 监视器利用系统分配的托管标识来授予对 Azure 密钥保管库的访问权限。系统分配的托管标识只能与单个 Azure 资源关联。 群集级别支持 Azure 监视器数据存储 （ADX 群集）的标识，这要求 CMK 功能在专用 ADX 群集上传递。 为了在多个工作区上支持 CMK，新的日志分析资源 （*群集*） 充当密钥保管库和日志分析工作区之间的中间标识连接。 此概念符合系统分配的标识约束，并且标识在 ADX 群集和日志分析*群集*资源之间保留，而所有关联的工作区的数据都使用密钥保管库密钥进行保护。 底层 ADX 群集存储使用与*群集*资源\'关联的托管标识通过 Azure 活动目录对 Azure 密钥保管库进行身份验证和访问。

![CMK 概述](media/customer-managed-keys/cmk-overview.png)
1.  客户密钥库。
2.  具有对密钥保管库权限的托管标识的客户日志分析*群集*资源 - 在数据存储 （ADX 群集） 级别支持该标识。
3.  Azure 监视器专用 ADX 群集。
4.  与 CMK 加密群集资源关联的客户工作区。

## <a name="encryption-keys-management"></a>加密密钥管理

存储数据加密涉及 3 种类型的密钥：

- **KEK** - 密钥保管库 （CMK） 中的密钥加密密钥
- **AEK** - 帐户加密密钥
- **DEK** - 数据加密密钥

下列规则适用：

- ADX 存储帐户为每个存储帐户（称为 AEK）生成唯一的加密密钥。

- AEK 用于派生 DEK，SDK 是用于加密写入磁盘的每个数据块的密钥。

- 在密钥保管库中配置密钥并在*群集*资源中引用密钥时，Azure 存储会向 Azure 密钥保管库发送请求，以包装和解包 AEK 以执行数据加密和解密操作。

- 您的 KEK 永远不会离开密钥保管库，如果是 HSM 密钥，它永远不会离开硬件。

- Azure 存储使用与*群集*资源关联的托管标识通过 Azure 活动目录对 Azure 密钥保管库进行身份验证和访问。

## <a name="cmk-provisioning-procedure"></a>CMK 预配过程

对于应用见解 CMK 配置，请按照附录内容执行步骤 3 和 6。

1. 订阅白名单 -- 此早期访问功能是必需的
2. 创建 Azure 密钥保管库并存储密钥
3. 创建*群集*资源
4. Azure 监视器数据存储 （ADX 群集） 预配
5. 向密钥保管库授予权限
6. 关联日志分析工作区

UI 当前不支持该过程，并且通过 REST API 执行预配过程。

> [!IMPORTANT]
> 任何 API 请求都必须在请求标头中包含承载授权令牌。

例如：

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

*其中 eyJ0eXAiO....* 

可以使用以下方法之一获取令牌：

1. 使用[应用注册](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法。

2. 在 Azure 门户中
    1. 在"开发人员工具 （F12） 中导航到 Azure 门户
    1. 在"批处理 api 版本"实例之一的"请求标头"下查找授权字符串。 它看起来像："授权：承载\<令牌"。\> 
    1. 根据以下示例复制并将其添加到 API 调用中。

3. 导航到 Azure REST 文档站点。 按任何 API 上的"试用"并复制承载令牌。

### <a name="subscription-whitelisting"></a>订阅白名单

CMK 功能是一种早期访问功能。 计划创建*群集*资源的订阅必须事先由 Azure 产品组列入白名单。 使用 Microsoft 的联系人提供订阅 ID。

> [!IMPORTANT]
> CMK 功能是区域性的。 Azure 密钥保管库、*群集*资源和关联的日志分析工作区必须位于同一区域中，但它们可以位于不同的订阅中。

### <a name="storing-encryption-key-kek"></a>存储加密密钥 （KEK）

创建或使用已必须生成的 Azure 密钥保管库，或导入用于数据加密的密钥。 Azure 密钥保管库必须配置为可恢复，以保护密钥和对 Azure 监视器中数据的访问。 您可以在密钥保管库中的属性下验证此配置，应启用*软删除*和*清除保护*。

这些设置可通过 CLI 和 PowerShell 获得：
- [软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)防护装置，防止强制删除机密/保管库，即使在软删除后

### <a name="create-cluster-resource"></a>创建*群集*资源

此资源用作密钥保管库和工作区之间的中间标识连接。 收到订阅已白名单的确认后，在工作区所在的区域创建日志分析*群集*资源。 应用程序见解和日志分析需要单独的*群集*资源类型。 *群集*资源的类型通过在创建时通过将"群集类型"属性设置为"LogAnalytics"或"应用程序见解"来定义。 群集资源类型后无法更改。

有关应用见解 CMK 配置，请遵循附录内容。

创建*群集*资源时，必须指定*群集*资源的容量预留级别 （sKU）。 容量预留级别可以在 1000 到 2000 之间，稍后可以以 100 步的步骤更新它。 如果您需要容量预留级别高于 2000，请联系 Microsoft 联系人以启用它。 此属性当前不会影响计费 - 一旦引入了专用群集的定价模型，计费将应用于任何现有的 CMK 部署。

**创建**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
标识在创建时分配给*群集*资源。

**响应**

202 已接受。 这是异步操作的标准资源管理器响应。

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure 监视器数据存储 （ADX 群集） 预配

在功能的早期访问期间，一旦完成前面的步骤，产品团队会手动预配 ADX 群集。 在提供*群集*资源响应的同时，使用 Microsoft 通道进行预配。 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> 复制并保存响应，因为在后面的步骤中需要这些详细信息

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
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

>[!Important]
> 完成不足 ADX 群集的预配需要几分钟时间。 *预配状态*值指示其状态，在预配时预配*帐户*，预配完成后为"成功"。
> "主体 Id" GUID 由*群集*资源的托管标识服务生成。

### <a name="grant-key-vault-permissions"></a>授予密钥保管库权限

使用向*群集*资源授予权限的新访问策略更新密钥保管库。 这些权限由下基础 Azure 监视器存储用于数据加密。 在 Azure 门户中打开密钥保管库，然后单击"访问策略"然后单击"+ 添加访问策略"以创建具有以下设置的策略：

- 密钥权限：选择"获取"，"包装键"和"取消包装密钥"权限。
- 选择主体：输入在上一步中响应中返回的主体 ID 值。

![授予密钥保管库权限](media/customer-managed-keys/grant-key-vault-permissions.png)

需要*Get*权限来验证密钥保管库是否配置为可恢复，以保护密钥和对 Azure 监视器数据的访问。

### <a name="update-cluster-resource-with-key-identifier-details"></a>使用密钥标识符详细信息更新群集资源

此步骤适用于密钥保管库中的初始和将来密钥版本更新。 它通知 Azure 监视器存储有关新的密钥版本。

要使用密钥保管库*密钥标识符*详细信息更新*群集*资源，请在 Azure 密钥保管库中选择密钥的当前版本，以获取有关密钥标识符详细信息。

![授予密钥保管库权限](media/customer-managed-keys/key-identifier-8bit.png)

使用密钥标识符详细信息更新*群集*资源 KeyVault 属性。

**更新**

>[!Warning]
> 您必须在*群集*资源更新中提供一个完整的正文，其中包括*标识**、sKU、KeyVault**属性*和*位置*。 缺少*KeyVault属性*详细信息将从*群集*资源中删除密钥标识符，并导致[密钥吊销](#cmk-kek-revocation)。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"KeyVault 属性"包含密钥保管库密钥标识符详细信息。

**响应**

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

### <a name="workspace-association-to-cluster-resource"></a>工作区关联到*群集*资源

对于应用见解 CMK 配置，请按照此步骤的附录内容操作。

> [!IMPORTANT]
> 此步骤应仅在 ADX 群集预配后执行。 如果在预配之前关联工作区和引入数据，则引入的数据将被删除，并且不可恢复。
> 要验证 ADX 群集是否预配，请执行*群集*资源获取 REST API 并检查*预配状态*值是否*成功*。

执行此操作（包括以下操作）需要对工作区和*群集*资源具有"写入"权限：

- 在工作区中：微软.操作见解/工作空间/写入
- 在*群集*资源中：微软.运营见解/集群/写入

**关联工作区**
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**响应**

```json
{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

工作区关联后，引入到工作区的数据将随托管密钥加密存储。

### <a name="workspace-association-verification"></a>工作区关联验证
您可以通过查看[工作区和获取](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)响应来验证工作区是否与*Custer*资源相关联。 关联的工作区将具有具有*群集*资源 ID 的"群集资源 Id"属性。

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

## <a name="cmk-kek-revocation"></a>CMK （KEK） 吊销

您可以通过禁用密钥或删除密钥保管库中的*群集*资源访问策略来撤消对数据的访问。 Azure 监视器存储始终在一小时内尊重密钥权限的更改，通常更快，并且存储将不可用。 引入到与*群集*资源关联的工作区的任何数据都将被删除，查询将失败。 以前引入的数据在 Azure 监视器存储中仍然无法访问，只要您的密钥被吊销，并且您的工作区不会被删除。 无法访问的数据受数据保留策略的约束，并在达到保留时清除。

存储将定期轮询密钥保管库，以尝试解包加密密钥，一旦访问，数据引入和查询将在 30 分钟内恢复。

## <a name="cmk-kek-rotation"></a>CMK （KEK） 旋转

CMK 的轮换需要使用 Azure 密钥保管库中的新密钥版本显式更新*群集*资源。 要使用新密钥版本更新 Azure 监视器，请按照"使用密钥标识符详细信息更新*群集*资源"步骤中的说明进行操作。 如果在密钥保管库中更新密钥版本，并且未在*群集*资源中更新新的密钥标识符详细信息，Azure 监视器存储将继续使用以前的密钥。
在密钥轮换操作（包括旋转之前和轮换后引入的数据）后，可以访问所有数据，因为所有数据仍由帐户加密密钥 （AEK） 加密，而这些数据现在由新的密钥加密密钥 （KEK） 版本加密。

## <a name="limitations-and-constraints"></a>限制和约束

- CMK 功能在 ADX 群集级别受支持，需要专用的 Azure 监视器 ADX 群集

- 每个订阅的最大*群集*资源数限制为 2

- 只有在您收到产品组确认 ADX 群集预配已完成后，才能将*群集*资源关联与工作区进行。 在此预配之前发送的数据将被删除，并且无法恢复。

- CMK 加密适用于 CMK 配置后新引入的数据。 在 CMK 配置之前引入的数据，仍使用 Microsoft 密钥进行加密。 您可以在 CMK 配置之前和之后无缝查询数据。

- 工作区与*群集*资源关联后，无法与*群集*资源取消关联，因为数据使用密钥加密，如果没有 Azure 密钥保管库中的 KEK，则无法访问。

- Azure 密钥保管库必须配置为可恢复。 默认情况下，这些属性未启用，应使用 CLI 和 PowerShell 进行配置：

  - [必须打开软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
  - [应打开清除保护](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)，以防止强制删除机密/保管库，即使在软删除后

- 应用程序见解和日志分析需要单独的*群集*资源。 *群集*资源的类型通过在创建时通过将"群集类型"属性设置为"LogAnalytics"或"应用程序见解"来定义。 无法更改*群集*资源类型。

- *群集*资源移动到其他资源组或订阅当前不受支持。

- Azure 密钥保管库、*群集*资源和相关工作区必须位于同一区域和同一 Azure 活动目录 （Azure AD） 租户中，但它们可以位于不同的订阅中。

- 如果群集资源关联到其他*群集*资源，则与*群集*资源的工作区关联将失败

## <a name="troubleshooting-and-management"></a>故障排除和管理

- 密钥保管库可用性
    - 在正常操作中，存储缓存 AEK 会定期返回密钥保管库以展开。
    
    - 瞬态连接错误。 存储通过允许密钥在缓存中停留一段时间来处理瞬态错误（超时、连接故障、DNS 问题），从而克服了可用性方面的任何小问题。 查询和引入功能不会中断地继续。
    
    - 实时站点，不可用约 30 分钟将导致存储帐户不可用。 查询功能不可用，并且使用 Microsoft 密钥缓存了几个小时的引入数据，以避免数据丢失。 还原对密钥保管库的访问后，查询将变为可用，临时缓存数据将被引入数据存储并使用 CMK 进行加密。

- 如果创建*群集*资源并立即指定 KeyVault 属性，则操作可能会失败，因为在将系统标识分配给*群集*资源之前无法定义访问策略。

- 如果使用 KeyVault 属性更新现有*群集*资源，并且密钥保管库中缺少"获取"密钥访问策略，则操作将失败。

- 如果尝试删除与工作区关联的*群集*资源，则删除操作将失败。

- 获取资源组的所有*群集*资源：

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

- 获取订阅的所有*群集*资源：

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **响应**
    
  与"资源组的*群集*资源"相同的响应，但在订阅范围内。
    
- 删除*群集*资源 -- 执行软删除操作，以便在 14 天内恢复*群集*资源、数据和相关工作区，无论删除是意外的还是有意的。 在软删除期间之后 *，群集资源和*数据不可恢复。 群集*资源*名称在软删除期间保持保留状态，无法创建具有该名称的新群集。

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常

- 恢复*群集*资源和数据 -- 在软删除期间，创建具有相同名称且位于同一订阅、资源组和区域中的*群集*资源。 按照**创建*群集*资源**步骤恢复*群集*资源。


## <a name="appendix"></a>附录

应用程序洞察客户托管密钥 （CMK） 也受支持，但应考虑以下更改，以帮助您规划针对应用程序洞察组件部署 CMK。

日志分析和应用程序见解使用相同的数据存储平台和查询引擎。 我们将通过应用程序见解集成到日志分析中，将这两个存储整合在一起，以便在第二季度提供 Azure 监视器下的单一统一日志存储。
2020. 此更改将使应用程序见解数据进入日志分析工作区，并使查询、见解和其他改进成为可能，而工作区上的 CMK 配置也将应用于您的应用程序见解数据。

> [!NOTE]
> 如果在集成之前不必为应用程序洞察数据部署 CMK，我们建议您等待使用应用程序见解 CMK，因为此类部署将因集成而中断，并且在迁移到 Log 后必须重新配置 CMK分析工作区。 每天 1TB 的最小值适用于群集级别，直到第二季度合并完成，应用程序见解和日志分析需要单独的群集。

## <a name="application-insights-cmk-configuration"></a>应用洞察 CMK 配置

应用见解 CMK 的配置与本文中说明的过程相同，包括约束和故障排除，这些步骤除外：

- 创建*群集*资源
- 将组件关联到*群集*资源

为应用程序见解配置 CMK 时，请使用以下步骤而不是上面列出的步骤。

### <a name="create-a-cluster-resource"></a>创建*群集*资源

此资源用作密钥保管库和组件之间的中间标识连接。 收到订阅已白名单的确认后，请在组件所在的区域创建日志分析*群集*资源。 *群集*资源的类型通过在创建时通过将*群集类型*属性设置为*LogAnalytics*或*应用程序见解*来定义。 它应该是应用程序*见解*的 CMK 应用程序见解。 配置后无法更改*群集类型*设置。

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

标识在创建时分配给*群集*资源。

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id" 
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"原则 id"是由托管标识服务生成的 GUID。

> [!IMPORTANT]
> 复制并保留"原则 id"值，因为在接下来的步骤中您将需要它。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>使用组件将组件关联到*群集*资源[- 创建或更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)API

执行此操作（包括以下操作）的组件和*群集*资源都需要具有"写入"权限：

- 组件：微软.见解/组件/写入
- 在*群集*资源中：微软.运营见解/集群/写入

> [!IMPORTANT]
> 此步骤应仅在 ADX 群集预配后执行。 如果在预配之前关联组件和引入数据，则引入的数据将被删除，并且不可恢复。
> 要验证 ADX 群集是否预配，请执行*群集*资源获取 REST API 并检查*预配状态*值是否*成功*。

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
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

**关联组件**

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
"群集定义 Id"是上一步骤的响应中提供的"群集 Id"值。
"好"的例子是"Web"。

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
"群集定义Id"是与此组件关联的*群集*资源 ID。

关联后，发送到组件的数据将随托管密钥进行加密存储。
