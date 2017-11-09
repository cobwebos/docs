---
title: "Azure Monitor（预览版）中的 Azure 事件中心指标 | Microsoft Docs"
description: "使用 Azure 监视来监视事件中心"
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: c3ee6a87592145801167b35ff281c04062e525da
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Azure Monitor（预览版）中的 Azure 事件中心指标

事件中心指标可提供 Azure 订阅中的事件中心资源状态。 通过多种指标数据集，可在命名空间和实体级别评估事件中心的总体运行状况。 由于它们能够帮助监视事件中心的状态，因此这些统计信息非常重要。 另外，指标也可帮助解决由根本原因造成的问题，而无需联系 Azure 支持。

Azure Monitor 提供统一的用户界面，用于监视各种 Azure 服务。 有关详细信息，请参阅 GitHub 上的[在 Microsoft Azure 中进行监视](../monitoring-and-diagnostics/monitoring-overview.md)和[通过 .NET 检索 Azure Monitor 指标](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)示例。

## <a name="access-metrics"></a>访问指标

Azure Monitor 提供多种访问指标的方法。 可从 [Azure 门户](https://portal.azure.com)、Azure Monitor API（REST 和 .Net）与分析解决方案（例如 Operation Management Suite 和事件中心）访问指标。 有关详细信息，请参阅 [Azure Monitor 指标](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api)。

默认情况下，已启用指标，并且可访问最近 30 天的数据。 如需将数据保留更长一段时间，可将指标数据存档到 Azure 存储帐户。 可在 Azure Monitor 的 [诊断设置](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) 中完成这种配置。

## <a name="access-metrics-in-the-portal"></a>在门户中访问指标

可在 [Azure 门户](https://portal.azure.com)中访问不同时间的指标。 以下示例演示如何在帐户级别查看成功的和传入的请求：

![][1]

也可通过命名空间直接访问指标。 为此，请选择命名空间，然后单击“指标（预览）”。 若要显示筛选到事件中心范围的指标，请选择事件中心，然后单击“指标（预览）”。

对于支持维度的指标，必须使用所需的维度值进行筛选，如以下示例所示：

![][2]

## <a name="billing"></a>计费

目前，在 Azure Monitor（预览版）中可免费使用指标。 但是，如果使用引入指标数据的其他解决方案，可能就需要收费。 例如，如果将指标数据存档到 Azure 存储帐户，则 Azure 存储会收费。 或者，如果将指标数据流式传输到 OMS 进行高级分析，Operation Management Suite (OMS) 则会进行收费。

以下指标可提供服务运行状况的概述。 

> [!NOTE]
> 当多个指标移动到不同的名称下，其将不受支持。 此时需要更新引用。 今后将不再支持标有“弃用的”关键字的指标。

所有指标值每隔一分钟发送到 Azure Monitor。 时间粒度定义呈现指标值的时间间隔。 所有事件中心指标所支持的时间间隔为一分钟。

## <a name="request-metrics"></a>请求指标

计算数据量和管理操作请求数。

| 指标名称 | 说明 |
| ------------------- | ----------------- |
| 传入的请求（预览） | 在指定期间内向 Azure 事件中心服务发送的请求数。 <br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName |
| 成功的请求（预览）   | 在指定期间内向 Azure 事件中心服务发送成功的请求数。 <br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName |
| 服务器错误（预览） | 由于 Azure 事件中心服务发生错误，在指定期间内未处理的请求数。 <br/><br/>单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName |
|用户错误（预览）|由于用户错误，在指定期间内未处理的请求数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|限制的请求（预览）|由于超出吞吐量单位用量而被限制的请求数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|超出配额的错误（预览）|超出可用配额的请求数。 有关事件中心配额的详细信息，请参阅[本文](event-hubs-quotas.md)。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="throughput-metrics"></a>吞吐量指标

| 指标名称 | 说明 |
| ------------------- | ----------------- |
|限制的请求（预览）|由于超出吞吐量单位用量而被限制的请求数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="message-metrics"></a>邮件指标

| 指标名称 | 说明 |
| ------------------- | ----------------- |
|传入的邮件（预览）|在指定期间内发送到事件中心的事件或邮件数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|待发邮件（预览）|在指定期间内从事件中心检索的事件或邮件数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|传入的字节（预览）|在指定期间内向 Azure 事件中心服务发送的字节数。<br/><br/> 单元：字节 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|待发字节（预览）|在指定期间内从 Azure 事件中心服务检索的字节数。<br/><br/> 单元：字节 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="connection-metrics"></a>连接指标

| 指标名称 | 说明 |
| ------------------- | ----------------- |
|活动连接（预览）|命名空间以及实体上的活动连接数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|打开的连接（预览）|打开的连接数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|关闭的连接（预览）|关闭的连接数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="event-hubs-capture-metrics"></a>事件中心捕获指标

当为事件中心启用捕获功能时，即可监视事件中心捕获指标。 以下指标描述了在启用捕获的情况下可监视的内容。

| 指标名称 | 说明 |
| ------------------- | ----------------- |
|捕获的积压工作（预览）|尚未捕获到所选目标的字节数。<br/><br/> 单元：字节 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|捕获的邮件（预览）|在指定期间内捕获到所选目标的邮件或事件数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|捕获的字节（预览）|在指定期间内捕获到所选目标的字节数。<br/><br/> 单元：字节 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="metrics-dimensions"></a>指标维度

Azure 事件中心支持对 Azure Monitor 中的指标使用以下维度。 为指标添加维度是可选项。 如果不添加维度，则在命名空间级别指定指标。 

| 指标名称 | 说明 |
| ------------------- | ----------------- |
|EntityName| 事件中心支持命名空间下的事件中心实体。|

## <a name="next-steps"></a>后续步骤

* 请参阅 GitHub 上的 [Azure 监视概述](../monitoring-and-diagnostics/monitoring-overview.md)。
* [通过 .NET 检索 Azure Monitor 指标](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)示例。 

有关事件中心的详细信息，请访问以下链接：

* 使用 [事件中心教程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中心常见问题](event-hubs-faq.md)
* [使用事件中心的示例应用程序](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



