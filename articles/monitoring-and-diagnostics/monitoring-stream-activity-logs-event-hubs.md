---
title: 将 Azure 活动日志流式传输到事件中心 | Microsoft Docs
description: 了解如何将 Azure 活动日志流式传输到事件中心。
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
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

若要更新活动日志的日志配置文件，使之包括流式传输，则执行更改的用户必须在事件中心授权规则中拥有 ListKey 权限。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，事件中心命名空间就不必与发出日志的订阅位于同一订阅中。

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

4. 选择“保存”保存这些设置。 这些设置会即时应用到订阅。
5. 如果有多个订阅，请重复此操作，并将所有数据发送至同一事件中心。

### <a name="via-powershell-cmdlets"></a>通过 PowerShell Cmdlet
如果日志配置文件已存在，则需先删除该配置文件。

1. 使用 `Get-AzureRmLogProfile` 确定日志配置文件是否存在。
2. 如果存在，使用 `Remove-AzureRmLogProfile` 将其删除。
3. 使用 `Set-AzureRmLogProfile` 创建配置文件：

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

服务总线规则 ID 是以下格式的字符串：`{service bus resource ID}/authorizationrules/{key name}`。 

### <a name="via-azure-cli"></a>通过 Azure CLI
如果日志配置文件已存在，则需先删除该配置文件。

1. 使用 `azure insights logprofile list` 确定日志配置文件是否存在。
2. 如果存在，使用 `azure insights logprofile delete` 将其删除。
3. 使用 `azure insights logprofile add` 创建配置文件：

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

服务总线规则 ID 是以下格式的字符串：`{service bus resource ID}/authorizationrules/{key name}`。

## <a name="consume-the-log-data-from-event-hubs"></a>使用事件中心的日志数据
[使用 Azure 活动日志监视订阅活动](monitoring-overview-activity-logs.md)中提供了活动日志的架构。 每个事件都采用 JSON blob（称为“记录”）数组的形式。

## <a name="next-steps"></a>后续步骤
* [将活动日志存档到存储帐户](monitoring-archive-activity-log.md)
* [阅读 Azure 活动日志概述](monitoring-overview-activity-logs.md)
* [根据活动日志事件设置警报](insights-auditlog-to-webhook-email.md)

