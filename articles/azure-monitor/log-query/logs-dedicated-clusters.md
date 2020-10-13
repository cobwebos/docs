---
title: Azure Monitor 记录专用群集
description: 对于超过 1 TB 的监视数据的客户，可以使用专用而不是共享群集
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 714a43ec197ac150488d4443c1eb6fe1be1da232
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575514"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor 记录专用群集

Azure Monitor 日志专用群集是一种部署选项，可用于更好地为大容量客户提供服务。 每天引入 4 TB 以上的数据的客户将使用专用群集。 具有专用群集的客户可以选择要在这些群集上托管的工作区。

除了对大容量的支持外，还可以使用专用群集的其他好处：

- **速率限制** -客户只能对专用群集具有更高的 [引入速率限制](../service-limits.md#data-ingestion-volume-rate) 。
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


## <a name="cluster-pricing-model"></a>群集定价模型

Log Analytics 专用群集使用至少 1000 GB/天的容量保留定价模型。 将按即用即付费率对超出预留级别的任何使用量进行计费。  [Azure Monitor 定价页]( https://azure.microsoft.com/pricing/details/monitor/)上提供了容量保留定价信息。  

群集容量预留级别通过使用下的参数以编程方式通过 Azure 资源管理器进行配置 `Capacity` `Sku` 。 `Capacity` 指定 GB 为单位，并且值可以为 1000 GB/天或更大，增量为 100 GB/天。

对于群集上的使用情况，有两种计费模式。 配置群集时，可通过 `billingType` 参数指定这些计费模式。 

1. **群集**：在此情况下（其为默认情况），引入数据的计费在群集级别完成。 将聚合与群集关联的每个工作区中的引入数据数量，以计算群集的每日账单。 

2. **工作区**：在为每个工作区的 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/) 中的每个节点分配记帐后，群集的容量保留成本将按节点分配给群集中的工作区 (。 ) 

请注意，如果工作区使用的是旧的每节点定价层，则当其链接到某个群集时，将基于针对群集容量预留的数据引入进行计费，并且不再按节点计费。 将继续应用 Azure 安全中心的每个节点的数据分配。

有关详细信息，请参阅 [此处]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)提供的 Log Analytics 专用群集的计费。


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

*调用* 
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

- 运行 Get-AzOperationalInsightsCluster 包含资源组名称的 PowerShell 命令，然后检查 ProvisioningState 属性。 此值在预配时 *ProvisioningAccount* ，并在完成时 *成功* 。
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

*调用*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
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

可以删除专用群集资源。 删除之前，必须取消群集中所有工作区的链接。 你需要对群集资源具有“写入”权限才能执行此操作。 

删除群集资源后，物理群集将进入清除和删除过程。 删除群集将删除存储在群集中的所有数据。 数据可以来自已链接到过去的群集的工作区。

过去 14 天内删除的群集资源处于软删除状态，因此该群集资源及其数据均可恢复。 由于所有工作区都已与*群集资源*删除*Cluster*解除关联，因此你需要在恢复后重新关联你的工作区。 用户无法执行恢复操作，请联系你的 Microsoft 渠道或支持恢复请求。

删除后的14天内，群集资源名称是保留名称，不能由其他资源使用。

**PowerShell**

使用以下 PowerShell 命令删除群集：

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

使用以下 REST 调用来删除群集：

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **响应**

  200 正常



## <a name="next-steps"></a>后续步骤

- 了解 [Log Analytics 专用群集计费](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- 了解 [Log Analytics 工作区的正确设计](../platform/design-logs-deployment.md)
