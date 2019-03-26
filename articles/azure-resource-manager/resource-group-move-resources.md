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
ms.date: 03/25/2019
ms.author: tomfitz
ms.openlocfilehash: 98236133a90cfddfe2ea476486556318b2c88b10
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418884"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>将资源移到新资源组或订阅中

本文说明了如何将 Azure 资源移动到另一 Azure 订阅，或移动到同一订阅下的另一资源组。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 移动资源。 若要完成教程，请参阅[教程：将 Azure 资源移到另一个资源组或订阅](./resource-manager-tutorial-move-resources.md)。

在移动操作过程中，源组和目标组都会锁定。 在完成移动之前，将阻止对资源组执行写入和删除操作。 此锁意味着将无法添加、更新或删除资源组中的资源，但并不意味着资源已被冻结。 例如，如果将 SQL Server 及其数据库移动到新的资源组中，则使用该数据库的应用程序将不会遇到停机的情况。 它仍可读取和写入到数据库。

移动资源仅能够将其移动到新的资源组。 移动操作不能更改该资源的位置。 新的资源组可能有不同的位置，但这不会更改该资源的位置。

> [!NOTE]
> 本文介绍如何在现有 Azure 订阅之间移动资源。 如果确实想要升级 Azure 订阅（例如从免费切换到即用即付），则需要转换订阅。
> * 若要升级免费试用版，请参阅[将免费试用版或 Microsoft Imagine Azure 订阅升级到即用即付](..//billing/billing-upgrade-azure-subscription.md)。
> * 若要更改即用即付帐户，请参阅[将 Azure 即用即付订阅更改为其他套餐](../billing/billing-how-to-switch-azure-offer.md)。
> * 如果无法转换订阅，请[创建 Azure 支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”作为问题类型。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>何时致电 Azure 支持人员

可以通过本文中所述的自助服务操作移动大部分资源。 使用自助服务操作可以：

* 移动 Resource Manager 资源。
* 根据[经典部署限制](#classic-deployment-limitations)移动经典资源。

有以下需要时，请联系[支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)：

* 将资源移到新的 Azure 帐户（和 Azure Active Directory 租户），并且对于上一部分中的说明需要帮助。
* 移动经典资源时遇到限制方面的问题。

## <a name="services-that-can-be-moved"></a>可以移动的服务

以下列表汇总提供了可移动到新资源组和订阅的 Azure 服务。 列表中的哪些资源类型支持移动，请参阅[移动对资源的操作支持](move-support-resources.md)。

* Analysis Services
* API 管理
* 应用服务应用（Web 应用）- 请参阅[应用服务限制](#app-service-limitations)
* 应用服务证书 - 请参阅[应用服务证书限制](#app-service-certificate-limitations)
* 自动化 - Runbook 必须与自动化帐户存在于同一资源组中。
* Azure Active Directory B2C
* Azure Redis 缓存 - 如果 Azure Redis 缓存实例配置了虚拟网络，则实例无法被移动到其他订阅。 请参阅[虚拟网络限制](#virtual-networks-limitations)。
* Azure Cosmos DB
* Azure 数据资源管理器
* Azure Database for MariaDB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure DevOps - 具有非 Microsoft 扩展购买的 Azure DevOps 组织必须先[取消其购买](https://go.microsoft.com/fwlink/?linkid=871160)，然后才能跨订阅移动帐户。
* Azure Maps
* Azure Monitor 日志
* Azure 中继
* Azure Stack - 注册
* Batch
* BizTalk 服务
* Bot 服务
* CDN
* 云服务 - 请参阅[经典部署限制](#classic-deployment-limitations)
* 认知服务
* 容器注册表 - 启用异地复制后无法移动容器注册表。
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
* Key Vault - 用于磁盘加密的 Key Vault 不能移动到同一订阅中的资源组，也不能跨订阅移动。
* 负载均衡器 - 可以移动基本 SKU 负载均衡器。 不能移动标准 SKU 负载均衡器。
* 逻辑应用
* 机器学习 - 机器学习工作室 Web 服务可以移动到同一订阅中的资源组，但不能移动到不同订阅中。 其他机器学习资源可以跨订阅进行移动。
* 托管磁盘-托管磁盘的可用性区域不能移动到其他订阅
* 托管标识 - 用户分配
* 媒体服务
* 监视器 - 确保移动到新订阅时，不会超出[订阅配额](../azure-subscription-service-limits.md#monitor-limits)
* 通知中心
* 操作见解
* 操作管理
* 门户仪表板
* Power BI - Power BI Embedded 和 Power BI 工作区集合
* 公共 IP - 可以移动基本 SKU 公共 IP。 不能移动标准 SKU 公共 IP。
* 恢复服务保管库 - 注册[预览版](#recovery-services-limitations)。
* 计划程序
* 搜索 - 不能一次性移动不同区域中的多个搜索资源。 只能通过多个单独的操作移动它们。
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
* 虚拟机-请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟机（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)
* 虚拟机规模集 - 请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟网络 - 请参阅[虚拟网络限制](#virtual-networks-limitations)
* VPN 网关

### <a name="services-that-cannot-be-moved"></a>无法移动的服务

以下列表汇总提供了不能移动到新资源组和订阅的 Azure 服务。 有关更为详细的信息，请参阅[支持移动操作的资源](move-support-resources.md)。

* AD 域服务
* AD 混合运行状况服务
* 应用程序网关
* Azure 数据库迁移
* Azure Databricks
* Azure 防火墙
* Azure Migrate
* Azure NetApp 文件
* 证书 - 应用服务证书可以移动，但上传的证书存在[限制](#app-service-limitations)。
* 经典应用程序
* 容器实例
* 容器服务
* Data Box
* Dev Spaces
* Dynamics LCS
* ExpressRoute
* Kubernetes 服务
* 实验室服务-教室实验室不能移动到新的资源组或订阅。 开发测试实验室可以移动到新的资源组在同一订阅中，但不是能跨订阅。
* 托管应用程序
* Microsoft 基因组学
* Azure 上的 SAP HANA
* 安全
* Site Recovery
* StorSimple 设备管理器
* 虚拟网络（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)

## <a name="limitations"></a>限制

此部分说明如何处理移动资源时的复杂方案。 限制如下：

* [虚拟机限制](#virtual-machines-limitations)
* [虚拟网络限制](#virtual-networks-limitations)
* [应用服务限制](#app-service-limitations)
* [应用服务证书限制](#app-service-certificate-limitations)
* [经典部署限制](#classic-deployment-limitations)
* [恢复服务限制](#recovery-services-limitations)
* [HDInsight 限制](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>虚拟机限制

您可以移动虚拟机使用托管的磁盘、 托管的映像、 托管的快照和可用性集与使用托管的磁盘的虚拟机。 可用性区域中的托管的磁盘不能移动到其他订阅。

以下方案尚不受支持：

* 证书存储在 Key Vault 中的虚拟机可以移动到同一订阅中的新资源组，但无法跨订阅进行移动。
* 不能移动标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 无法跨资源组或订阅移动基于附加了计划的市场资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。

若要移动使用 Azure 备份配置的虚拟机，请使用以下解决方法：

* 找到虚拟机的位置。
* 找到含有以下命名模式的资源组：`AzureBackupRG_<location of your VM>_1` 例如，AzureBackupRG_westus2_1
* 如果在 Azure 门户中，则查看“显示隐藏的类型”
* 如果在 PowerShell 中，则使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* 如果在 CLI 中，则使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 使用类型 `Microsoft.Compute/restorePointCollections` 找到具有命名模式 `AzureBackup_<name of your VM that you're trying to move>_###########` 的资源
* 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
* 删除完成后，即可移动虚拟机。 可以将保管库和虚拟机移到目标订阅。 移动后即可继续备份，不会丢失数据。
* 若要了解如何移动恢复服务保管库以完成备份，请参阅[恢复服务限制](#recovery-services-limitations)。

### <a name="virtual-networks-limitations"></a>虚拟网络限制

移动虚拟网络时，还必须移动其从属资源。 对于 VPN 网关，必须移动 IP 地址、虚拟网络网关和所有关联的连接资源。 本地网络网关可以位于不同的资源组中。

若要与网络接口卡一起移动虚拟机，必须移动所有从属资源。 你必须移动网络接口卡的虚拟网络、 虚拟网络和 VPN 网关中的所有其他网络接口卡。

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Azure Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

### <a name="app-service-limitations"></a>应用服务限制

移动应用服务资源的限制各不相同，具体取决于是在订阅内移动资源，还是将应用服务资源移到新的订阅。 如果 Web 应用使用应用服务证书，请参阅[应用服务证书限制](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>在同一订阅中移动

在同一订阅中移动 Web 应用时，无法移动第三方 SSL 证书。 不过，可以将 Web 应用移动到新的资源组而不移动其第三方证书，并且，应用的 SSL 功能仍然可以工作。

如果希望随 Web 应用移动 SSL 证书，请执行以下步骤：

1. 从 Web 应用中删除第三方证书，但保留证书的副本
2. 移动 Web 应用。
3. 将第三方证书上传到移动后的 Web 应用。

#### <a name="moving-across-subscriptions"></a>跨订阅移动

_在订阅之间_移动 Web 应用时存在以下限制：

- 目标资源组中不能有任何现有的应用服务资源。 应用服务资源包括：
    - Web 应用
    - 应用服务计划
    - 上传或导入的 SSL 证书
    - 应用服务环境
- 资源组中的所有应用服务资源必须一起移动。
- 只能从最初创建应用服务资源的资源组中移动它们。 如果某个应用服务资源不再位于其原始资源组中，则必须首先将其移动回该原始资源组，然后才能将其在订阅之间移动。

### <a name="app-service-certificate-limitations"></a>应用服务证书限制

可将应用服务证书移动到新的资源组或订阅。 如果应用服务证书已绑定到某个 Web 应用，必须先执行一些步骤，然后才能将资源移到新的订阅中。 移动资源之前，请在 Web 应用中删除 SSL 绑定和专用证书。 应用服务证书不需删除，只需删除 Web 应用中的专用证书。

### <a name="classic-deployment-limitations"></a>经典部署限制

移动通过经典模型部署的资源时，其选项各不相同，具体取决于是在订阅内移动资源，还是将资源移到新的订阅。

#### <a name="same-subscription"></a>相同的订阅

在同一订阅内将资源从一个资源组移到另一个资源组时存在以下限制：

* 不能移动虚拟网络（经典）。
* 虚拟机（经典）必须与云服务一起移动。
* 移动云服务时，必须移动其所有虚拟机。
* 一次只能移动一项云服务。
* 一次只能移动一个存储帐户（经典）。
* 存储帐户（经典）与虚拟机或云服务不能在同一操作中移动。

要将经典资源移到同一订阅内的新资源组，请通过[门户](#use-portal)、Azure PowerShell、Azure CLI 或 REST API 使用标准移动操作。 使用的操作应与移动 Resource Manager 资源时所用的操作相同。

#### <a name="new-subscription"></a>新订阅

将资源移到新订阅时存在以下限制：

* 必须在同一操作中移动订阅中的所有经典资源。
* 目标订阅不得包含任何其他经典资源。
* 只能通过独立的适用于经典移动的 REST API 来请求移动。 将经典资源移到新订阅时，不能使用标准的资源管理器移动命令。

要将经典资源移动到新订阅，请使用特定于经典资源的 REST 操作。 若要使用 REST，请执行以下步骤：

1. 检查源订阅是否可以参与跨订阅移动。 使用以下操作：

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在请求正文中包括：

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

### <a name="recovery-services-limitations"></a>恢复服务限制

 若要移动恢复服务保管库，则必须注册[受限公共预览版](../backup/backup-azure-move-recovery-services-vault.md)。

目前，每个区域一次可以移动一个恢复服务保管库。 不能移动在 IaaS 虚拟机中备份 Azure 文件、Azure 文件同步或 SQL 的保管库。

如果虚拟机不随保管库移动，则当前虚拟机恢复点会保留在保管库中，直至过期。 不管虚拟机是否随保管库移动，均可根据保管库的备份历史记录还原虚拟机。

恢复服务保管库不支持跨订阅备份。 如果跨订阅移动保管库和虚拟机备份数据，则必须将虚拟机移到同一订阅，并使用同一目标资源组来继续备份。

在保管库移动后，会保留为保管库定义的备份策略。 在移动后，必须再次为保管库设置报告和监视功能。

若要将虚拟机移到新的订阅而不移动恢复服务保管库，请执行以下操作：

 1. 暂时停止备份
 1. [删除还原点](#virtual-machines-limitations)。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
 1. 将虚拟机移到新的订阅
 1. 在该订阅的新保管库中对其重新进行保护

移动不支持用于使用 Azure Site Recovery 设置灾难恢复的“存储”、“网络”或“计算”资源。 例如，假设已设置将本地计算机复制到存储帐户 (Storage1)，并且想要受保护的计算机在故障转移到 Azure 之后显示为连接到虚拟网络 (Network1) 的虚拟机 (VM1)。 不能在同一订阅中的资源组之间或在订阅之间移动这些 Azure 资源 - Storage1、VM1 和 Network1。

### <a name="hdinsight-limitations"></a>HDInsight 限制

可以将 HDInsight 群集移到新的订阅或资源组。 但是，无法在订阅之间移动链接到 HDInsight 群集的网络资源（例如虚拟网络、NIC 或负载均衡器）。 此外，无法将连接到群集的虚拟机的 NIC 移到新的资源组。

将 HDInsight 群集移到新的订阅时，首先移动其他资源（如存储帐户）。 然后移动 HDInsight 群集本身。

## <a name="checklist-before-moving-resources"></a>移动资源前需查看的清单

移动资源之前需执行的一些重要步骤。 验证这些条件可以避免错误。

1. 源订阅和目标订阅必须处于活动状态。 如果在启用已禁用的帐户时遇到问题，请[创建 Azure 支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”作为问题类型。

1. 源订阅与目标订阅必须在同一个 [Azure Active Directory 租户](../active-directory/develop/quickstart-create-new-tenant.md)中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 Azure PowerShell 或 Azure CLI。

   对于 Azure PowerShell，请使用：

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   对于 Azure CLI，请使用：

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   如果源订阅和目标订阅的租户 ID 不相同，可使用以下方法协调租户 ID：

   * [将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)
   * [如何将 Azure 订阅关联或添加到 Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必须针对要移动的资源的资源提供程序注册目标订阅。 否则会出现错误“未针对资源类型注册订阅”。 将资源移到新的订阅时，可能会看到此错误，但该订阅从未配合该资源类型使用。

   对于 PowerShell，请使用以下命令来获取注册状态：

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   若要注册资源提供程序，请使用：

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
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

1. 在可能的情况下，将大型移动分为单独的移动操作。 在一次操作中有 800 多项资源时，资源管理器会立即返回错误。 但是，移动 800 项以下的资源也可能因超时而失败。

1. 该服务必须支持移动资源的功能。 若要确定移动是否会成功，[验证你的移动请求](#validate-move)。 请参阅本文中的以下部分，了解[支持对资源进行移动的服务](#services-that-can-be-moved)和[不支持对资源进行移动的服务](#services-that-cannot-be-moved)。

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
 "resources": ["<resource-id-1>", "<resource-id-2>"],
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

## <a name="move-resources"></a>移动资源

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />使用 Azure 门户

若要移动资源，请选择包含这些资源的资源组，然后选择“移动”按钮。

![移动资源](./media/resource-group-move-resources/select-move.png)

选择是要将资源移到新资源组还是新订阅。

选择要移动的资源和目标资源组。 确认需要更新这些资源的脚本，选择“确定”。 如果在上一步中已选择“编辑订阅”图标，则还必须选择目标订阅。

![选择目标](./media/resource-group-move-resources/select-destination.png)

在“通知”中，可以看到移动操作正在运行。

![显示移动状态](./media/resource-group-move-resources/show-status.png)

操作完成后，你会获得结果通知。

![显示移动结果](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>使用 Azure PowerShell

要将现有资源移到另一个资源组或订阅，请使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下面的示例演示了如何将多个资源移动到新的资源组。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移到新订阅，请包含 `DestinationSubscriptionId` 参数的值。

### <a name="by-using-azure-cli"></a>使用 Azure CLI

若要将现有资源移动到另一个资源组或订阅，请使用 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 命令。 提供要移动的资源的资源 ID。 下面的示例演示了如何将多个资源移动到新的资源组。 在 `--ids` 参数中，提供要移动的资源 ID 的空格分隔列表。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

### <a name="by-using-rest-api"></a>使用 REST API

要将现有资源移到另一个资源组或订阅中，请运行：

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在请求正文中，指定目标资源组和要移动的资源。 有关移动 REST 操作的详细信息，请参阅[移动资源](/rest/api/resources/Resources/MoveResources)。

## <a name="next-steps"></a>后续步骤

* 若要了解有关用于管理你的资源的 PowerShell cmdlet 的信息，请参阅[使用 Azure PowerShell 与 Resource Manager](manage-resources-powershell.md)。
* 若要了解有关 Azure CLI 命令用于管理资源的信息，请参阅[使用 Azure CLI 使用资源管理器](manage-resources-cli.md)。
* 若要了解管理订阅所需的门户功能，请参阅[使用 Azure 门户管理资源](resource-group-portal.md)。
* 若要了解如何向资源应用逻辑组织，请参阅[使用标记组织资源](resource-group-using-tags.md)。
