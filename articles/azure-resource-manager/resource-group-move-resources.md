---
title: "将 Azure 资源移到新的订阅或资源组 | Microsoft 文档"
description: "使用 Azure Resource Manager 将资源移到新的资源组或订阅。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: tomfitz
ms.openlocfilehash: 326039c58466e65183a594e222db24e998f151b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>将资源移到新资源组或订阅中

本文说明了如何将资源移动到新订阅，或移动到同一个订阅中的新资源组。 可以使用门户、PowerShell、Azure CLI 或 REST API 移动资源。 无需 Azure 支持人员的任何协助，即可使用本文中所述的移动操作。

在移动资源的过程中，源组和目标组都会锁定。 在完成移动之前，将阻止对资源组执行写入和删除操作。 此锁意味着将无法添加、更新或删除资源组中的资源，但并不意味着资源已被冻结。 例如，如果将 SQL Server 及其数据库移动到新的资源组中，则使用该数据库的应用程序将不会遇到停机的情况。 它仍可读取和写入到数据库。

不能更改该资源的位置。 移动资源仅能够将其移动到新的资源组。 新的资源组可能有不同的位置，但这不会更改该资源的位置。

> [!NOTE]
> 本文介绍如何在现有 Azure 帐户产品/服务中移动资源。 如果确实想要更改 Azure 帐户产品（如从即用即付升级到预付），同时继续使用现有资源，请参阅 [Switch your Azure subscription to another offer](../billing/billing-how-to-switch-azure-offer.md)（将 Azure 订阅切换到其他产品）。
>
>

## <a name="checklist-before-moving-resources"></a>移动资源前需查看的清单

移动资源之前需执行的一些重要步骤。 验证这些条件可以避免错误。

1. 源订阅与目标订阅必须在同一个 [Azure Active Directory 租户](../active-directory/active-directory-howto-tenant.md)中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 Azure PowerShell 或 Azure CLI。

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

  如果源和目标订阅的租户 ID 不相同，则必须联系[支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)才能将资源移动到新租户。

2. 该服务必须支持移动资源的功能。 本文列出了支持对资源进行移动的服务和不支持对资源进行移动的服务。
3. 必须针对要移动的资源的资源提供程序注册目标订阅。 否则会出现错误“未针对资源类型注册订阅”。 将资源移到新的订阅时，可能会遇到此问题，但该订阅从未配合该资源类型使用。

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

## <a name="when-to-call-support"></a>何时致电支持人员

可以通过本文中所述的自助服务操作移动大部分资源。 使用自助服务操作可以：

* 移动 Resource Manager 资源。
* 根据[经典部署限制](#classic-deployment-limitations)移动经典资源。

有以下需要时，请联系[支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)：

* 将资源移到新 Azure 帐户（和 Azure Active Directory 租户）。
* 移动经典资源时遇到限制方面的问题。

## <a name="services-that-enable-move"></a>可移动的服务

支持同时移动到新资源组和订阅的服务包括：

* API 管理
* 应用服务应用（Web 应用）- 请参阅[应用服务限制](#app-service-limitations)
* Application Insights
* 自动化
* Azure Cosmos DB
* 批处理
* 必应地图
* CDN
* 云服务 - 请参阅[经典部署限制](#classic-deployment-limitations)
* 认知服务
* 内容审查器
* 数据目录
* Data Factory
* 数据湖分析
* Data Lake Store
* DNS
* 事件中心
* HDInsight 群集 - 请参阅 [HDInsight 限制](#hdinsight-limitations)
* IoT 中心
* 密钥保管库
* 负载均衡器
* 逻辑应用
* 机器学习
* 媒体服务
* Mobile Engagement
* 通知中心
* 操作见解
* 操作管理
* Power BI
* Redis 缓存
* 计划程序
* 搜索
* 服务器管理
* 服务总线
* Service Fabric
* 存储
* 存储（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)
* 流分析 - 当流分析作业处于运行状态时，则无法进行移动。
* SQL 数据库服务器 - 数据库和服务器必须位于同一个资源组中。 当移动 SQL 服务器时，其所有数据库也会一起移动。
* 流量管理器
* 虚拟机 - 包含托管磁盘的 VM 无法移动。 请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟机（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)
* 虚拟机规模集 - 请参阅[虚拟机限制](#virtual-machines-limitations)
* 虚拟网络 - 请参阅[虚拟网络限制](#virtual-networks-limitations)
* VPN 网关

## <a name="services-that-do-not-enable-move"></a>不支持移动的服务

当前不支持对资源进行移动的服务包括：

* AD 域服务
* AD 混合运行状况服务
* 应用程序网关
* BizTalk 服务
* 容器服务
* Express Route
* 开发测试实验室 - 支持移动到同一订阅中的新资源组，但不支持跨订阅移动。
* Dynamics LCS
* 托管应用程序
* 托管磁盘 - 请参阅[虚拟机限制](#virtual-machines-limitations)
* 恢复服务保管库 - 此外，也不可以移动与恢复服务保管库关联的计算、网络和存储资源，请参阅[恢复服务限制](#recovery-services-limitations)。
* 安全
* StorSimple 设备管理器
* 虚拟网络（经典）- 请参阅[经典部署限制](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>虚拟机限制

托管磁盘不支持移动。 此限制意味着，多个相关资源也无法移动。 无法移动：

* 托管磁盘
* 包含托管磁盘的虚拟机
* 基于托管磁盘创建的映像
* 基于托管磁盘创建的快照
* 包含托管磁盘的虚拟机的可用性集

无法在订阅之间移动基于 Marketplace 资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。

证书存储在 Key Vault 中的虚拟机可以移动到同一订阅中的新资源组，但无法跨订阅进行移动。

## <a name="virtual-networks-limitations"></a>虚拟网络限制

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

## <a name="app-service-limitations"></a>应用服务限制

使用应用服务应用时，不能只移动应用服务计划。 若要移动应用服务应用，可以使用以下选项：

* 将该资源组中的应用服务计划以及所有其他应用服务资源移到尚无应用服务资源的新资源组。 这一要求意味着，与应用服务计划不关联的应用服务资源也必须移动。
* 将应用移到另一个资源组中，但保留原始资源组中的所有应用服务计划。

应用服务计划不需要与应用位于同一资源组中，应用也可以正常运行。

例如，如果资源组包含：

* **web-a**，与 **plan-a** 相关联
* **web-b**，与 **plan-b** 相关联

选项包括：

* 移动 **web-a**、**plan-a**、**web-b** 和 **plan-b**
* 移动 **web-a** 和 **web-b**
* 移动 **web-a**
* 移动 **web-b**

所有其他组合都涉及保留在移动应用服务计划时不能保留的资源类型（任何应用服务资源类型）。

如果 Web 应用与其应用服务计划位于不同的资源组中，而你想要将二者都移到新的资源组，则必须分两步执行移动操作。 例如：

* **web-a** 位于 **web-group** 中
* **plan-a** 位于 **plan-group** 中
* 想要让 **web-a** 和 **plan-a** 位于 **combined-group** 中

若要完成此移动操作，可按以下顺序执行两个独立的移动操作：

1. 将 **web-a** 移到 **plan-group** 中
2. 将 **web-a** 和 **plan-a** 移到 **combined-group** 中。

可将应用服务证书移动到新的资源组或订阅，且不会出现任何问题。 但是，如果 Web 应用包括从外部购买并上传到应用的 SSL 证书，则移动 Web 应用之前必须删除该证书。 例如，可以执行以下步骤：

1. 从 Web 应用中删除已上传的证书
2. 移动 Web 应用
3. 将证书上传到 Web 应用

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
* 只能通过独立的适用于经典移动的 REST API 来请求移动。 将经典资源移到新订阅时，不能使用标准的 Resource Manager 移动命令。

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

## <a name="hdinsight-limitations"></a>HDInsight 限制

可以将 HDInsight 群集移到新的订阅或资源组。 但是，无法在订阅之间移动链接到 HDInsight 群集的网络资源（例如虚拟网络、网卡或负载均衡器）。 此外，无法将连接到群集的虚拟机的网卡移到新的资源组。

将 HDInsight 群集移到新的订阅时，首先移动其他资源（如存储帐户）。 然后移动 HDInsight 群集本身。

## <a name="use-portal"></a>使用门户

要移动资源，请选择包含这些资源的资源组，并选择“移动”按钮。

![移动资源](./media/resource-group-move-resources/select-move.png)

选择是要将资源移到新资源组还是新订阅。

选择要移动的资源和目标资源组。 确认需要更新这些资源的脚本，选择“确定”。 如果在上一步中已选择“编辑订阅”图标，则还必须选择目标订阅。

![选择目标](./media/resource-group-move-resources/select-destination.png)

在“通知”中，可以看到移动操作正在运行。

![显示移动状态](./media/resource-group-move-resources/show-status.png)

操作完成后，会获得结果通知。

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

若要将现有资源移动到另一个资源组或订阅，请使用 [az resource move](/cli/azure/resource?view=azure-cli-latest#az_resource_move) 命令。 提供要移动的资源的资源 ID。 下面的示例演示了如何将多个资源移动到新的资源组。 在 `--ids` 参数中，提供要移动的资源 ID 的空格分隔列表。

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
