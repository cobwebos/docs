---
title: 使用 Azure 诊断与 Azure Application Insights 的集成针对 Azure 云服务中的问题发出警报 | Microsoft Docs
description: 使用 Azure Application Insights 监视 Azure 云服务中的问题，例如启动故障、崩溃和角色回收循环
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: harelbr; mbullwin
ms.openlocfilehash: 18817fd84a86a72d379f96973b71658f2cdf4afd
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851051"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>使用 Azure 诊断与 Azure Application Insights 的集成针对 Azure 云服务中的问题发出警报

在本文中，我们将介绍如何设置警报规则，用以监视 Azure 云服务（Web 和辅助角色）中的问题，例如启动故障、崩溃和角色回收循环。

本文中介绍的方法基于 [Azure 诊断与 Application Insights 的集成](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)以及最新发布的 [Application Insights 日志警报](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)功能。

## <a name="define-a-base-query"></a>定义基础查询

若要开始使用，我们将定义一个基础查询，用以从 Windows Azure 通道检索 Windows 事件日志事件，这些事件将作为跟踪记录捕获到 Application Insights 中。
可以使用这些记录检测 Azure 云服务中的各种问题，例如启动故障、运行时故障和回收循环。

> [!NOTE]
> 下面的基础查询检查 30 分钟时间范围内的问题，并且假定在引入遥测数据记录时有 10 分钟的延迟。 可以根据你的需要配置这些默认值。

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>针对特定事件 ID 进行检查

在检索 Windows 事件日志事件后，可以通过根据相应的事件 ID 和消息属性进行检查来检测特定问题（参阅下面的示例）。
只需要将上面的基础查询与下面的查询之一组合起来，并且在定义日志警报规则时使用该组合后的查询。

> [!NOTE]
> 在下面的示例中，如果在分析时间范围内发现三个以上事件，则会检测到问题。 可以对此默认值进行配置来更改警报规则的敏感度。

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>创建警报

在 Application Insights 资源的导航菜单中，转到“警报”，然后选择“新建警报规则”。

![创建规则时的屏幕截图](./media/app-insights-proactive-cloud-services/001.png)

在“创建规则”窗口中，在“定义警报条件”部分下，单击“添加条件”，然后选择“自定义日志搜索”。

![为警报定义条件时的屏幕截图](./media/app-insights-proactive-cloud-services/002.png)

在“搜索查询”框中，粘贴你在前面的步骤中编制的组合查询。

然后，前进到“阈值”框并将其值设置为 0。 还可以调整“期间”和“频率”字段。
单击“完成”。

![配置信号逻辑查询的屏幕截图](./media/app-insights-proactive-cloud-services/003.png)

在“定义警报详细信息”部分下，为警报规则提供**名称**和**说明**，并设置其**严重性**。
另外，请确保将“创建后启用规则”按钮设置为“是”。

![警报详细信息屏幕截图](./media/app-insights-proactive-cloud-services/004.png)

在“定义操作组”部分下，可以选择一个现有**操作组**，也可以新建一个。
可以选择让操作组包含各种类型的多个操作。

![操作组屏幕截图](./media/app-insights-proactive-cloud-services/005.png)

定义操作组后，确认更改并单击“创建警报规则”。

## <a name="next-steps"></a>后续步骤

了解有关自动检测的详细信息：

[故障异常](app-insights-proactive-failure-diagnostics.md)
[内存泄露](app-insights-proactive-potential-memory-leak.md)
[性能异常](app-insights-proactive-performance-diagnostics.md)

