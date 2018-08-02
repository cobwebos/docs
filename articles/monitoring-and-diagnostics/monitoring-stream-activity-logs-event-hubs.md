---
title: 将 Azure 活动日志流式传输到事件中心
description: 了解如何将 Azure 活动日志流式传输到事件中心。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 7a5372174fcc7cd9552c00c9d283772c9863b815
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257992"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>将 Azure 活动日志流式传输到事件中心
可以选择下列两种方式之一将 [Azure 活动日志](monitoring-overview-activity-logs.md)准实时流式传输到任何应用程序：

* 使用门户中内置的“导出”选项
* 通过 Azure PowerShell cmdlets 或 Azure CLI 在日志文件中启用 Azure 服务总线规则 ID

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>可以对活动日志和事件中心执行的操作
可以通过下述两种方式将流式传输功能用于活动日志：

* **流式传输到第三方日志记录和遥测系统**：一段时间后，Azure 事件中心的流式传输就会成为一种机制，用于将活动日志通过管道传输到第三方 SIEM 和 Log Analytics 解决方案。
* **生成自定义遥测和日志记录平台**：如果已经有一个自定义生成的遥测平台，或者正想生成一个，则可利用事件中心高度可缩放的发布-订阅功能，灵活地引入活动日志。 有关详细信息，请参阅 [Dan Rosanova 的视频：了解如何在全局规模的遥测平台中使用事件中心](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="enable-streaming-of-the-activity-log"></a>启用活动日志的流式传输
可以通过编程方式或门户启用活动日志的流式传输。 无论通过哪种方式，你都要选择一个事件中心命名空间，以及该命名空间的共享访问策略。 发生第一个新的活动日志事件时，将在该命名空间中创建名为“insights-logs-operationallogs”的事件中心。 

如果没有事件中心命名空间，则需要先创建一个。 如果先前已将活动日志事件流式传输到此事件中心命名空间，则将重用先前创建的事件中心。 

共享访问策略定义了流式传输机制具有的权限。 目前，流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 在门户中针对事件中心命名空间的“配置”选项卡下，可以创建或修改事件中心命名空间的共享访问策略。 

若要更新活动日志的日志配置文件，使之包括流式传输，则执行更改的用户必须在事件中心授权规则中拥有 ListKey 权限。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限并且这两个订阅都在同一个 AAD 租户中，事件中心命名空间就不必与发出日志的订阅位于同一订阅中。

### <a name="via-the-azure-portal"></a>通过 Azure 门户
1. 使用门户左侧的“全部”服务搜索浏览至“活动日志”部分。
   
   ![从门户中的服务列表中选择活动日志](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. 选择日志顶部的“导出”按钮。
   
   ![门户中的“导出”按钮](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   注意，在之前的视图中查看活动日志时所应用的设置对你的导出设置没有任何影响。 这些设置仅仅用于在门户中浏览活动日志时筛选查看的内容。
3. 在显示出的部分选择“所有区域”。 不要选择特定的区域。
   
   ![导出部分](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > 如果选择了任何“所有区域”以外的选项，你将遗漏想要接收的关键事件。 活动日志是全局性（非区域性）日志，因此大多数事件并不具备相关联的区域。 
   >

4. 单击“Azure 事件中心”选项，并选择应发送日志的事件中心命名空间，然后单击“确定”。
5. 选择“保存”保存这些设置。 这些设置会即时应用到订阅。
6. 如果有多个订阅，请重复此操作，并将所有数据发送至同一事件中心。

### <a name="via-powershell-cmdlets"></a>通过 PowerShell Cmdlet
如果日志配置文件已存在，首先需要删除现有日志配置文件，然后创建新的日志配置文件。

1. 使用 `Get-AzureRmLogProfile` 确定日志配置文件是否存在。  如果存在日志配置文件，请找到 *name* 属性。
2. 使用 `Remove-AzureRmLogProfile` 通过 *name* 属性的值删除日志配置文件。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. 使用 `Add-AzureRmLogProfile` 创建新的日志配置文件：

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>通过 Azure CLI
如果日志配置文件已存在，首先需要删除现有日志配置文件，然后创建新的日志配置文件。

1. 使用 `az monitor log-profiles list` 确定日志配置文件是否存在。
2. 使用 `az monitor log-profiles delete --name "<log profile name>` 通过 *name* 属性的值删除日志配置文件。
3. 使用 `az monitor log-profiles create` 创建新的日志配置文件：

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>使用事件中心的日志数据
[使用 Azure 活动日志监视订阅活动](monitoring-overview-activity-logs.md)中提供了活动日志的架构。 每个事件都采用 JSON blob（称为“记录”）数组的形式。

## <a name="next-steps"></a>后续步骤
* [将活动日志存档到存储帐户](monitoring-archive-activity-log.md)
* [阅读 Azure 活动日志概述](monitoring-overview-activity-logs.md)
* [根据活动日志事件设置警报](insights-auditlog-to-webhook-email.md)

