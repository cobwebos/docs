---
title: Azure Monitor 记录专用群集
description: 对于超过 1 TB 的监视数据的客户，可以使用专用而不是共享群集
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 4ad3aa7169fcf7eeda6e56a2eab6669b8783d77d
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461455"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor 记录专用群集

Azure Monitor 日志专用群集是一种部署选项，可用于更好地为大容量客户提供服务。 每天引入 4 TB 以上的数据的客户将使用专用群集。 具有专用群集的客户可以选择要在这些群集上托管的工作区。

除了对大容量的支持外，还可以使用专用群集的其他好处：

- **速率限制** -客户只能对专用群集具有更高的引入速率限制。
- **功能** -某些企业功能仅适用于专用群集-具体而言， (CMK) 和密码箱支持的客户托管密钥。 
- **一致性** -客户具有自己的专用资源，因此不会影响在同一共享基础结构上运行的其他客户。
- **成本效率** -使用专用群集可能更具成本效益，因为分配的容量预留层将考虑所有的群集引入，并将其应用到所有的工作区，即使其中一些工作区较小且不符合容量保留折扣。
- 如果所有工作区都在同一群集上，则**跨工作区**查询运行速度更快。

专用群集要求客户每天使用至少 1 TB 的数据引入容量进行提交。 迁移到专用群集非常简单。 无数据丢失或服务中断。 

> [!IMPORTANT]
> 专用群集经过批准并完全支持生产部署。 但是，由于临时容量限制，我们要求你预先注册才能使用该功能。 在 Microsoft 中使用联系人提供订阅 ID。

## <a name="management"></a>管理 

专用群集通过代表 Azure Monitor 日志群集的 Azure 资源进行管理。 使用 PowerShell 或 REST API 对此资源执行所有操作。

创建群集后，可以对其进行配置并将工作区链接到该群集。 当工作区链接到群集时，发送到工作区的新数据将驻留在群集上。 只有与群集位于同一区域的工作区可以链接到群集。 可以从群集中 unliked 工作区，但有一些限制。 本文包含有关这些限制的更多详细信息。 

群集级别上的所有操作都需要对群集的 " `Microsoft.OperationalInsights/clusters/write` 操作" 权限。 可以通过包含操作的所有者或参与者或 `*/write` 包含操作的 Log Analytics 参与者角色授予此权限 `Microsoft.OperationalInsights/*` 。 有关 Log Analytics 权限的详细信息，请参阅 [在 Azure Monitor 中管理对日志数据和工作区的访问权限](../platform/manage-access.md)。 

## <a name="billing"></a>计费

只有在具有或没有容量保留层的情况下使用每 GB 计划的工作区才支持专用群集。 对于承诺为此类群集引入超过 1 TB 的客户，专用群集不收取额外费用。 "提交到引入" 意味着它们在群集级别分配了至少 1 TB/天的容量预留层。 虽然在群集级别附加了容量保留，但对于数据的实际收费有两个选项：

- *群集* (默认) -群集的容量保留成本归属于 *群集* 资源。
- *工作区* -群集的容量保留成本与群集中的工作区按比例进行了分类。 如果该天的引入总数据量低于容量预留，则会对该 *群集* 资源进行计费。 请参阅 [Log Analytics 专用群集](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) ，了解群集定价模型的详细信息。

有关专用群集计费的详细信息，请参阅 [Log Analytics 专用群集计费](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)。

## <a name="creating-a-cluster"></a>创建群集

首先创建群集资源，开始创建专用群集。

必须指定以下属性：

- **ClusterName**：用于管理目的。 不会向用户公开此名称。
- **ResourceGroupName**：对于任何 Azure 资源，群集都属于某个资源组。 建议使用中央 IT 资源组，因为群集通常由组织中的许多团队共享。 有关更多设计注意事项，请查看 [设计 Azure Monitor 日志部署](../platform/design-logs-deployment.md)
- **位置**：群集位于特定的 Azure 区域中。 只有位于此区域的工作区可以链接到此群集。
- **SkuCapacity**：创建*群集*资源时，必须指定 (sku) 的*容量保留*级别。 *容量预留*级别可以在 1000 gb 到 3000 gb 之间。 如果需要，可以在以后的100中更新。 如果需要的容量预留级别超过 3000 GB，请联系我们 LAIngestionRate@microsoft.com 。 有关群集成本的详细信息，请参阅 [管理 Log Analytics 群集的成本](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

创建 *群集* 资源后，可以编辑其他属性，例如 *Sku*、* keyVaultProperties 或 *billingType*。 参阅下面的更多详细信息。

> [!WARNING]
> 群集创建触发资源分配和设置。 此操作最长可能需要一小时才能完成。 建议以异步方式运行。

创建群集的用户帐户必须具有标准 Azure 资源创建权限： `Microsoft.Resources/deployments/*` 和群集写入权限 `(Microsoft.OperationalInsights/clusters/write)` 。

### <a name="create"></a>创建 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*拨* 
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

*响应*

应为 200 OK 和标头。

### <a name="check-provisioning-status"></a>检查预配状态

设置 Log Analytics 群集需要一段时间才能完成。 可以通过多种方式检查预配状态：

- 运行 AzOperationalInsightsCluster PowerShell 命令，并提供资源组名称，并检查 ProvisioningState 属性。 此值在预配时 *ProvisioningAccount* ，并在完成时 *成功* 。
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。

- 在群集资源上发送 GET 请求，然后查看 provisioningState 值 。 此值在预配时 *ProvisioningAccount* ，并在完成时 *成功* 。

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*PrincipalId* GUID 由*群集*资源的托管标识服务生成。

## <a name="change-cluster-properties"></a>更改群集属性

创建 *群集* 资源并对其进行完全预配后，可以使用 PowerShell 或 REST API 在群集级别编辑其他属性。 除了在群集创建期间可用的属性以外，还可以在预配群集后设置其他属性：

- **keyVaultProperties**：用于配置用于预配 [Azure Monitor 客户管理的密钥](../platform/customer-managed-keys.md#cmk-provisioning-procedure)的 Azure Key Vault。 它包含以下参数：  *KeyVaultUri*、 *KeyName*、 *KeyVersion*。 
- **billingType** - *billingType* 属性确定 *群集* 资源及其数据的计费归属：
- **群集** (默认) -群集的容量保留成本归属于 *群集* 资源。
- **工作区** -群集的容量预留成本与群集中的工作区按比例进行了分类，如果当天的总引入数据低于容量预留，则会向 *群集* 资源计费一些使用情况。 请参阅 [Log Analytics 专用群集](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) ，了解群集定价模型的详细信息。 

> [!NOTE]
> PowerShell 不支持 *billingType* 属性。
> 群集属性更新可能会执行异步，可能需要一段时间才能完成。

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> 可以使用 PATCH 更新 *群集* 资源 *sku*、 *keyVaultProperties* 或 *billingType* 。

例如： 

*拨*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
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

*响应*

200正常和标头

### <a name="check-cluster-update-status"></a>检查群集更新状态

密钥标识符的传播需要几分钟才能完成。 可以通过两种方式检查更新状态：

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。 

   OR

- 在群集资源上发送 GET 请求，然后查看 KeyVaultProperties 属性 。 你最近更新的密钥标识符详细信息应返回到响应中。

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
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>将工作区链接到群集

当工作区链接到专用群集时，引入到工作区中的新数据将路由到新群集，而现有的数据仍保留在现有群集上。 如果使用客户管理的密钥对专用群集进行加密 (CMK) ，则只使用密钥对新数据进行加密。 系统将从用户抽象出这种差异，用户只需像平常一样查询工作区，系统就会在后端执行跨群集查询。

一个群集最多可以链接到100个工作区。 链接工作区与群集位于同一区域。 若要保护系统后端并避免数据碎片，每个月不能将一个工作区链接到两个以上的群集。

若要执行链接操作，需要对工作区和 *群集* 资源具有 "写入" 权限：

- 在工作区中： *microsoft.operationalinsights/工作区/写入*
- 在 *群集* 资源中： *microsoft.operationalinsights/群集/写入*

除了计费方面以外，链接的工作区仍保留其自己的设置，例如数据保留长度。
工作区和群集可以位于不同的订阅中。 如果使用 Azure Lighthouse 将这两个用户映射到单个租户，则工作区和群集可能位于不同的租户中。

作为任何群集操作，仅在完成 Log Analytics 群集设置后，才可以执行链接工作区。

> [!WARNING]
> 将工作区链接到群集需要同步多个后端组件并确保缓存混合。 此操作最多可能需要两个小时才能完成。 建议以异步方式运行。


### <a name="link-operations"></a>链接操作

**PowerShell**

使用以下 PowerShell 命令链接到群集：

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

使用以下 REST 调用链接到群集：

*发送*

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

*响应*

200 OK 和标头。

### <a name="using-customer-managed-keys-with-linking"></a>将客户托管的密钥用于链接

如果使用客户管理的密钥，则在关联操作完成后，引入数据将使用托管密钥进行加密存储，这可能需要长达90分钟的时间才能完成。 

可以通过两种方式检查工作区关联状态：

- 从响应中复制 Azure-AsyncOperation URL 值，并进行异步操作状态检查。

- 发送 [工作区–获取](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 请求并观察响应。 关联的工作区的 "功能" 下有一个 clusterResourceId。

发送请求如下所示：

*发送*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*响应*

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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>从专用群集取消链接工作区

你可以从群集中取消链接工作区。 从群集中取消链接工作区后，与此工作区关联的新数据不会发送到专用群集。 此外，不能再通过群集来计费工作区。 未链接的工作区的旧数据可能会保留在群集上。 如果使用客户管理的密钥对此数据进行加密 (CMK) ，则会保留 Key Vault 机密。 系统从 Log Analytics 用户中提取此更改。 用户可以像平常一样直接查询工作区。 系统会根据需要在后端执行跨群集查询，无需向用户指示。  

> [!WARNING] 
> 一个月内每个工作区的链接操作数限制为两个。 请花些时间来考虑并相应地计划取消链接操作。 

## <a name="delete-a-dedicated-cluster"></a>删除专用群集

可以删除专用群集资源。 删除之前，必须取消群集中所有工作区的链接。 删除群集资源后，物理群集将进入清除和删除过程。 删除群集将删除存储在群集中的所有数据。 数据可以来自已链接到过去的群集的工作区。

## <a name="next-steps"></a>后续步骤

- 了解 [Log Analytics 专用群集计费](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- 了解 [Log Analytics 工作区的正确设计](../platform/design-logs-deployment.md)
