---
title: 将 Azure 资源移到新的订阅或资源组 | Microsoft 文档
description: 使用 Azure 资源管理器将资源移到新的资源组或订阅。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: tomfitz
ms.openlocfilehash: 33d5560f2bfef04678cf7a2236fd920385d68aac
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452150"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>将资源移到新资源组或订阅中

本文说明了如何将资源移动到新订阅，或移动到同一个订阅中的新资源组。 可以使用门户、PowerShell、Azure CLI 或 REST API 移动资源。 无需 Azure 支持人员的任何协助，即可使用本文中所述的移动操作。

在移动资源的过程中，源组和目标组都会锁定。 在完成移动之前，将阻止对资源组执行写入和删除操作。 此锁意味着将无法添加、更新或删除资源组中的资源，但并不意味着资源已被冻结。 例如，如果将 SQL Server 及其数据库移动到新的资源组中，则使用该数据库的应用程序将不会遇到停机的情况。 它仍可读取和写入到数据库。

不能更改该资源的位置。 移动资源仅能够将其移动到新的资源组。 新的资源组可能有不同的位置，但这不会更改该资源的位置。

> [!NOTE]
> 本文介绍如何在现有 Azure 帐户产品/服务中移动资源。 如果确实想要更改 Azure 帐户产品/服务（如从即用即付升级到预付），同时继续使用现有资源，请参阅 [Switch your Azure subscription to another offer](../billing/billing-how-to-switch-azure-offer.md)（将 Azure 订阅切换到其他套餐）。
>
>

## <a name="checklist-before-moving-resources"></a>移动资源前需查看的清单

移动资源之前需执行的一些重要步骤。 验证这些条件可以避免错误。

1. 源订阅与目标订阅必须在同一个 [Azure Active Directory 租户](../active-directory/develop/quickstart-create-new-tenant.md)中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 Azure PowerShell 或 Azure CLI。

  对于 Azure PowerShell，请使用：

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  对于 Azure CLI，请使用：

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  如果源订阅和目标订阅的租户 ID 不相同，可使用以下方法协调租户 ID：

  * [将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)
  * [如何将 Azure 订阅关联或添加到 Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必须针对要移动的资源的资源提供程序注册目标订阅。 否则会出现错误“未针对资源类型注册订阅”。 将资源移到新的订阅时，可能会遇到此问题，但该订阅从未配合该资源类型使用。

  对于 PowerShell，请使用以下命令来获取注册状态：

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  若要注册资源提供程序，请使用：

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  对于 Azure CLI，请使用以下命令来获取注册状态：

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  若要注册资源提供程序，请使用：

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

1. 移动资源的帐户至少需要具备下列权限：

   * 源资源组上的 Microsoft.Resources/subscriptions/resourceGroups/moveResources/action 权限。
   * 目标资源组上的 Microsoft.Resources/subscriptions/resourceGroups/write 权限。

1. 在移动资源之前，请检查要将资源移动到的订阅的订阅配额。 如果移动资源意味着订阅将超出其限制，则需要检查是否可以请求增加配额。 有关限制的列表及如何请求增加配额的信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。

1. 在可能的情况下，将大型移动分为单独的移动操作。 在一次操作中尝试移动超过 800 项资源，资源管理器将立即失败。 但是，移动 800 项以下的资源也可能因超时而失败。

1. 该服务必须支持移动资源的功能。 若要确定移动是否会成功，[验证你的移动请求](#validate-move)。 请参阅本文中的以下部分，了解[支持对资源进行移动的服务](#services-that-can-be-moved)和[不支持对资源进行移动的服务](#services-that-cannot-be-moved)。

## <a name="when-to-call-support"></a>何时致电支持人员

可以通过本文中所述的自助服务操作移动大部分资源。 使用自助服务操作可以：

* 移动 Resource Manager 资源。
* 根据[经典部署限制](#classic-deployment-limitations)移动经典资源。

有以下需要时，请联系[支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)：

* 将资源移到新的 Azure 帐户（和 Azure Active Directory 租户），并且对于上一部分中的说明需要帮助。
* 移动经典资源时遇到限制方面的问题。

## <a name="validate-move"></a>验证移动

[验证移动操作](/rest/api/resources/resources/validatemoveresources)可以测试你的移动方案而无需实际移动资源。 使用此操作来确定移动是否会成功。 若要运行此操作，需要：

* 源资源组的名称
* 目标资源组的资源 ID
* 要移动的每个资源的资源 ID
* 你的帐户的[访问令牌](/rest/api/azure/#acquire-an-access-token)

发送以下请求：

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

包含请求正文：

```json
{
 "resources": ['<resource-id-1>', '<resource-id-2>'],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果请求格式正确，则操作将返回：

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 状态代码指示已接受验证请求，但尚未确定移动操作是否会成功。 `location` 值包含用于检查长时间运行操作的状态的 URL。  

若要检查状态，请发送以下请求：

```
GET <location-url>
Authorization: Bearer <access-token>
```

操作仍在运行时，会继续收到 202 状态代码。 请等待 `retry-after` 值中所示的秒数，然后重试。 如果移动操作验证成功，则会收到 204 状态代码。 如果移动验证失败，则会收到错误消息，例如：

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="services-that-can-be-moved"></a>可以移动的服务

以下列表汇总提供了可移动到新资源组和订阅的 Azure 服务。 有关更为详细的信息，请参阅[支持移动操作的资源](move-support-resources.md)。

* Analysis Services
* API 管理
* 应用服务应用（Web 应用）- 请参阅[应用服务限制](#app-service-limitations)
* 应用服务证书
* Application Insights
* 自动化
* Azure Active Directory B2C
* Azure Cosmos DB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure DevOps - 具有非 Microsoft 扩展购买的 Azure DevOps 组织必须先[取消其购买](https://go.microsoft.com/fwlink/?linkid=871160)，然后才能跨订阅移动帐户。
* Azure Maps
* Azure 中继
* Azure Stack - 注册
* Batch
* BizTalk 服务
* Bot 服务
* CDN
* 云服务 - 请参阅[经典部署限制](#classic-deployment-limitations)
* 认知服务
* 容器注册表
* 内容审查器
* 成本管理
* Customer Insights
* 数据目录
* 数据工厂
* Data Lake Analytics
* Data Lake Store
* DNS
* 事件网格
* 事件中心
* Front Door
* HDInsight 群集 - 请参阅 [HDInsight 限制](#hdinsight-limitations)
* IoT 中心
* IoT 中心
* Key Vault
* 负载均衡器 - 请参阅[负载均衡器限制](#lb-limitations)
* Log Analytics
* 逻辑应用
* 机器学习 - 机器学习工作室 Web 服务可以移动到同一订阅中的资源组，但不能移动到不同订阅中。 其他机器学习资源可以跨订阅进行移动。
* 托管磁盘 - 请参阅[虚拟机针对约束的限制](#virtual-machines-limitations)
* 托管标识 - 用户分配
* 媒体服务
* 通知中心
* 操作见解
* 操作管理
* 门户仪表板
* Power BI - Power BI Embedded 和 Power BI 工作区集合
* 公共 IP - 请参阅[公共 IP 限制](#pip-limitations)
* Redis 缓存 - 如果 Redis 缓存实例配置了虚拟网络，则实例无法被移动到其他订阅。 请参阅[虚拟网络限制](#virtual-networks-limitations)。
* 计划程序
* 搜索
* 服务总线
* Service Fabric
* Service Fabric 网格
* SignalR 服务
* 存储 - 不同区域中的存储帐户不能在同一操作中移动。 相反，为每个区域使用单独的操作。
* 存储（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)
* 流分析 - 当流分析作业处于运行状态时，则无法进行移动。
* SQL 数据库服务器 - 数据库和服务器必须位于同一个资源组中。 当移动 SQL 服务器时，其所有数据库也会一起移动。 此行为适用于 Azure SQL 数据库和 Azure SQL 数据仓库数据库。
* 时序见解
* 流量管理器
* 虚拟机 - 针对使用托管磁盘的虚拟机，请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟机（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)
* 虚拟机规模集 - 请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟网络 - 请参阅[虚拟网络限制](#virtual-networks-limitations)
* VPN 网关

## <a name="services-that-cannot-be-moved"></a>无法移动的服务

以下列表汇总提供了不能移动到新资源组和订阅的 Azure 服务。 有关更为详细的信息，请参阅[支持移动操作的资源](move-support-resources.md)。

* AD 域服务
* AD 混合运行状况服务
* 应用程序网关
* Azure 数据库迁移
* Azure Databricks
* Azure Migrate
* Batch AI
* 证书 - 应用服务证书可以移动，但上传的证书存在[限制](#app-service-limitations)。
* 容器实例
* 容器服务
* Data Box
* Dev Spaces
* Dynamics LCS
* Express Route
* Kubernetes 服务
* 实验室服务 - 支持移动到同一订阅中的新资源组，但不支持跨订阅移动。
* 负载均衡器 - 请参阅[负载均衡器限制](#lb-limitations)
* 托管应用程序
* Microsoft 基因组学
* NetApp
* 公共 IP - 请参阅[公共 IP 限制](#pip-limitations)
* 恢复服务保管库 - 此外，也不可以移动与恢复服务保管库关联的计算、网络和存储资源，请参阅[恢复服务限制](#recovery-services-limitations)。
* Azure 上的 SAP HANA
* 安全
* Site Recovery
* StorSimple 设备管理器
* 虚拟网络（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>虚拟机限制

从 2018 年 9 月 24 日起，支持移动托管磁盘。 

1. 你必须注册才能启用此功能。

  ```azurepowershell-interactive
  Register-AzureRmProviderFeature -FeatureName ManagedResourcesMove -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az feature register --namespace Microsoft.Compute --name ManagedResourcesMove
  ```

1. 注册请求初始返回状态为 `Registering`。 你可以使用以下方法查看当前状态：

  ```azurepowershell-interactive
  Get-AzureRmProviderFeature -FeatureName ManagedResourcesMove -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az feature show --namespace Microsoft.Compute --name ManagedResourcesMove
  ```

1. 请等待几分钟的时间，使状态更改为 `Registered`。

1. 注册该功能后，注册 `Microsoft.Compute` 资源提供程序。 请执行此步骤，即使以前已注册过资源提供程序。

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az provider register --namespace Microsoft.Compute
  ```

此支持意味着你还可以移动：

* 包含托管磁盘的虚拟机
* 托管映像
* 托管快照
* 包含托管磁盘的虚拟机的可用性集

以下是尚不支持的约束：

* 证书存储在 Key Vault 中的虚拟机可以移动到同一订阅中的新资源组，但无法跨订阅进行移动。
* 使用 Azure 备份配置的虚拟机。 使用以下解决方法移动这些虚拟机
  * 找到虚拟机的位置。
  * 找到含有以下命名模式的资源组：`AzureBackupRG_<location of your VM>_1` 例如，AzureBackupRG_westus2_1
  * 如果在 Azure 门户中，则查看“显示隐藏的类型”
  * 如果在 PowerShell 中，则使用 `Get-AzureRmResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
  * 如果在 CLI 中，则使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
  * 现在，使用类型 `Microsoft.Compute/restorePointCollections` 找到具有命名模式 `AzureBackup_<name of your VM that you're trying to move>_###########` 的资源
  * 删除此资源
  * 删除完成后，即可移动虚拟机
* 无法移动具有标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集
* 无法跨资源组或订阅移动基于附加了计划的市场资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。


## <a name="virtual-networks-limitations"></a>虚拟网络限制

移动虚拟网络时，还必须移动其从属资源。 对于 VPN 网关，必须移动 IP 地址、虚拟网络网关和所有关联的连接资源。 本地网络网关可以位于不同的资源组中。

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

## <a name="app-service-limitations"></a>应用服务限制

移动应用服务资源的限制各不相同，具体取决于是在订阅内移动资源，还是将应用服务资源移到新的订阅。

这些部分中所述的限制适用于已上传的证书，不适用于应用服务证书。 可将应用服务证书移动到新的资源组或订阅，且不会存在任何限制。 如果你有多个使用相同应用服务证书的 Web 应用，请先移动所有这些 Web 应用，然后再移动证书。

### <a name="moving-within-the-same-subscription"></a>在同一订阅中移动

_在同一订阅中_移动 Web 应用时，无法移动已上传的 SSL 证书。 不过，可以将 Web 应用移动到新的资源组而不移动其已上传的 SSL 证书，并且，应用的 SSL 功能仍然可以工作。

如果希望随 Web 应用移动 SSL 证书，请执行以下步骤：

1.  从 Web 应用中删除已上传的证书
2.  移动 Web 应用。
3.  将证书上传到移动后的 Web 应用。

### <a name="moving-across-subscriptions"></a>跨订阅移动

_在订阅之间_移动 Web 应用时存在以下限制：

- 目标资源组中不能有任何现有的应用服务资源。 应用服务资源包括：
    - Web 应用
    - 应用服务计划
    - 上传或导入的 SSL 证书
    - 应用服务环境
- 资源组中的所有应用服务资源必须一起移动。
- 只能从最初创建应用服务资源的资源组中移动它们。 如果某个应用服务资源不再位于其原始资源组中，则必须首先将其移动回该原始资源组，然后才能将其在订阅之间移动。

## <a name="classic-deployment-limitations"></a>经典部署限制

移动通过经典模型部署的资源时，其选项各不相同，具体取决于是在订阅内移动资源，还是将资源移到新的订阅。

### <a name="same-subscription"></a>相同的订阅

在同一订阅内将资源从一个资源组移到另一个资源组时存在以下限制：

* 不能移动虚拟网络（经典）。
* 虚拟机（经典）必须与云服务一起移动。
* 移动云服务时，必须移动其所有虚拟机。
* 一次只能移动一项云服务。
* 一次只能移动一个存储帐户（经典）。
* 存储帐户（经典）与虚拟机或云服务不能在同一操作中移动。

要将经典资源移到同一订阅内的新资源组，请通过[门户](#use-portal)、[Azure PowerShell](#use-powershell)、[Azure CLI](#use-azure-cli) 或 [REST API](#use-rest-api) 使用标准移动操作。 使用的操作应与移动 Resource Manager 资源时所用的操作相同。

### <a name="new-subscription"></a>新订阅

将资源移到新订阅时存在以下限制：

* 必须在同一操作中移动订阅中的所有经典资源。
* 目标订阅不得包含任何其他经典资源。
* 只能通过独立的适用于经典移动的 REST API 来请求移动。 将经典资源移到新订阅时，不能使用标准的资源管理器移动命令。

要将经典资源移动到新订阅，请使用特定于经典资源的 REST 操作。 若要使用 REST，请执行以下步骤：

1. 检查源订阅是否可以参与跨订阅移动。 使用以下操作：

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     在请求正文中包含以下内容：

  ```json
  {
    "role": "source"
  }
  ```

     验证操作的响应采用以下格式：

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. 检查目标订阅是否可以参与跨订阅移动。 使用以下操作：

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     在请求正文中包含以下内容：

  ```json
  {
    "role": "target"
  }
  ```

     响应的格式与源订阅验证相同。
3. 如果两个订阅都通过了验证，可使用以下操作将所有经典资源从一个订阅移到另一个订阅：

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    在请求正文中包含以下内容：

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

运行该操作可能需要几分钟。

## <a name="recovery-services-limitations"></a>恢复服务限制

移动不支持用于使用 Azure Site Recovery 设置灾难恢复的“存储”、“网络”或“计算”资源。

例如，假设已设置将本地计算机复制到存储帐户 (Storage1)，并且想要受保护的计算机在故障转移到 Azure 之后显示为连接到虚拟网络 (Network1) 的虚拟机 (VM1)。 不能在同一订阅中的资源组之间或在订阅之间移动这些 Azure 资源 - Storage1、VM1 和 Network1。

若要在资源组之间移动在 Azure 备份中注册的 VM：
 1. 暂时停止备份并保留备份数据
 2. 将 VM 移至目标资源组
 3. 在相同/新保管库中对其进行重新保护，用户可以从在移动操作之前创建的可用还原点进行还原。
如果用户跨订阅移动备份 VM，则步骤 1 和步骤 2 保持相同。 在步骤 3 中，用户需要在目标订阅中存在/创建的新保管库下保护 VM。 恢复服务保管库不支持跨订阅备份。

## <a name="hdinsight-limitations"></a>HDInsight 限制

可以将 HDInsight 群集移到新的订阅或资源组。 但是，无法在订阅之间移动链接到 HDInsight 群集的网络资源（例如虚拟网络、NIC 或负载均衡器）。 此外，无法将连接到群集的虚拟机的 NIC 移到新的资源组。

将 HDInsight 群集移到新的订阅时，首先移动其他资源（如存储帐户）。 然后移动 HDInsight 群集本身。

## <a name="search-limitations"></a>搜索限制

不能同时移动置于不同区域中的多个搜索资源。
在这种情况下，需要分别移动它们。

## <a name="lb-limitations"></a> 负载均衡器限制

可以移动基本 SKU 负载均衡器。
不能移动标准 SKU 负载均衡器。

## <a name="pip-limitations"></a> 公共 IP 限制

可以移动基本 SKU 公共 IP。
不能移动标准 SKU 公共 IP。

## <a name="use-portal"></a>使用门户

要移动资源，请选择包含这些资源的资源组，并选择“移动”按钮。

![移动资源](./media/resource-group-move-resources/select-move.png)

选择是要将资源移到新资源组还是新订阅。

选择要移动的资源和目标资源组。 确认需要更新这些资源的脚本，选择“确定”。 如果在上一步中已选择“编辑订阅”图标，则还必须选择目标订阅。

![选择目标](./media/resource-group-move-resources/select-destination.png)

在“通知”中，可以看到移动操作正在运行。

![显示移动状态](./media/resource-group-move-resources/show-status.png)

操作完成后，你会获得结果通知。

![显示移动结果](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>使用 PowerShell

要将现有资源移到另一个资源组或订阅，请使用 [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) 命令。 下面的示例演示了如何将多个资源移动到新的资源组。

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移到新订阅，请包含 `DestinationSubscriptionId` 参数的值。

## <a name="use-azure-cli"></a>使用 Azure CLI

若要将现有资源移动到另一个资源组或订阅，请使用 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 命令。 提供要移动的资源的资源 ID。 下面的示例演示了如何将多个资源移动到新的资源组。 在 `--ids` 参数中，提供要移动的资源 ID 的空格分隔列表。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

## <a name="use-rest-api"></a>使用 REST API

要将现有资源移到另一个资源组或订阅中，请运行：

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在请求正文中，指定目标资源组和要移动的资源。 有关移动 REST 操作的详细信息，请参阅[移动资源](/rest/api/resources/Resources/MoveResources)。

## <a name="next-steps"></a>后续步骤

* 要了解管理订阅所需的 PowerShell cmdlet，请参阅[将 Azure PowerShell 与 Resource Manager 配合使用](powershell-azure-resource-manager.md)。
* 要了解管理订阅所需的 Azure CLI 命令，请参阅[将 Azure CLI 与 Resource Manager 配合使用](xplat-cli-azure-resource-manager.md)。
* 若要了解管理订阅所需的门户功能，请参阅[使用 Azure 门户管理资源](resource-group-portal.md)。
* 若要了解如何向资源应用逻辑组织，请参阅[使用标记组织资源](resource-group-using-tags.md)。
