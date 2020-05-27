---
title: Azure Monitor 中的 Azure 中继指标 | Microsoft Docs
description: 本文介绍如何使用 Azure Monitor 监视 Azure 中继的状态。
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211796"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Monitor 中的 Azure 中继指标 
Azure 中继指标提供 Azure 订阅中的资源的状态。 通过一组丰富的指标数据，可在命名空间和实体级别评估中继资源的总体运行状况。 这些统计信息非常重要，因为它们能够帮助监视 Azure 中继的状态。 另外，指标也可帮助解决由根本原因造成的问题，而无需联系 Azure 支持。

Azure Monitor 提供了统一的用户界面，可用于监视各种 Azure 服务。 有关详细信息，请参阅 GitHub 上的[在 Microsoft Azure 中进行监视](../monitoring-and-diagnostics/monitoring-overview.md)和[通过 .NET 检索 Azure Monitor 指标](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)示例。

> [!IMPORTANT]
> 本文仅适用于 Azure 中继的混合连接功能，而不适用于 WCF 中继。 

## <a name="access-metrics"></a>访问指标

Azure Monitor 提供多种访问指标的方法。 可从 [Azure 门户](https://portal.azure.com)、Azure Monitor API（REST 和 .Net）与分析解决方案（例如 Operation Management Suite 和事件中心）访问指标。 有关详细信息，请参阅 [Azure Monitor 收集的监视数据](../azure-monitor/platform/data-platform.md)。

默认情况下，已启用指标，并且可访问最近 30 天的数据。 如需将数据保留更长一段时间，可将指标数据存档到 Azure 存储帐户。 可在 Azure Monitor 的 [诊断设置](../azure-monitor/platform/diagnostic-settings.md) 中完成这种配置。

## <a name="access-metrics-in-the-portal"></a>在门户中访问指标

可在 [Azure 门户](https://portal.azure.com)中监视一段时间内的指标。 以下示例演示了如何在帐户级别查看成功的请求和传入的请求：

![][1]

也可以直接通过命名空间来访问指标。 为此，请选择命名空间，然后单击“指标”。 

对于支持维度的指标，必须使用所需的维度值进行筛选。

## <a name="billing"></a>计费

在预览版中，目前可免费使用 Azure Monitor 中的指标。 但是，如果使用引入指标数据的其他解决方案，可能就需要收费。 例如，如果将指标数据存档到 Azure 存储帐户，则 Azure 存储会收费。 如果将指标数据流式传输到 Azure Monitor 日志进行高级分析，则 Azure Monitor 日志也会向你收费。

以下指标可提供服务运行状况的概述。 

> [!NOTE]
> 我们弃用了多个指标，因为它们已移动到不同的名称下。 这可能会要求你更新引用。 今后将不再支持标有“弃用的”关键字的指标。

所有指标值每隔一分钟发送到 Azure Monitor。 时间粒度定义了提供指标值的时间间隔。 所有 Azure 中继指标支持的时间间隔都是 1 分钟。

## <a name="connection-metrics"></a>连接指标

| 标准名称 | 说明 |
| ------------------- | ----------------- |
| ListenerConnections-Success  | 在指定的期间内成功与 Azure 中继建立的侦听器连接数。 <br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|ListenerConnections-ClientError |在指定的期间内在侦听器连接上发生的客户端错误数。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|ListenerConnections-ServerError |在指定的期间内在侦听器连接上发生的服务器错误数。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|SenderConnections-Success |在指定的期间内成功建立的发送者连接数。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|SenderConnections-ClientError |在指定的期间内在发送者连接上发生的客户端错误数。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|SenderConnections-ServerError |在指定的期间内在发送者连接上发生的服务器错误数。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|ListenerConnections-TotalRequests |指定的期间内的侦听器连接总数<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|SenderConnections-TotalRequests |在指定的期间内由发送者发出的连接请求数。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|ActiveConnections |活动连接的数目。 此值为时间点值。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|ActiveListeners |活动侦听器的数目。 此值为时间点值。<br/><br/> 单位：Count <br/> 聚合类型：总计 <br/> 维度：EntityName|
|ListenerDisconnects |指定的期间内断开连接的侦听器数。<br/><br/> 单位：字节 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|SenderDisconnects |指定的期间内断开连接的发送者数。<br/><br/> 单位：字节 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="memory-usage-metrics"></a>内存使用情况指标

| 标准名称 | 说明 |
| ------------------- | ----------------- |
|BytesTransferred |在指定的期间内传输的字节数。<br/><br/> 单位：字节 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="metrics-dimensions"></a>指标维度

Azure 中继支持为 Azure Monitor 中的指标使用以下维度。 为指标添加维度是可选的。 如果不添加维度，则指标是在命名空间级别指定的。 

|维度名称|说明|
| ------------------- | ----------------- |
|EntityName| Azure 中继支持命名空间下的消息实体。|

## <a name="next-steps"></a>后续步骤

请参阅 [Azure 监视概述](../monitoring-and-diagnostics/monitoring-overview.md)。

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




