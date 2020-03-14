---
title: Azure Monitor 中的 Azure 服务总线指标 |Microsoft Docs
description: 本文介绍如何使用 Azure Monitor 来监视服务总线实体（队列、主题和订阅）。
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 329b930c950ea7c58bdac798fce51af152aa8ff3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260964"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Monitor 中的 Azure 服务总线指标

服务总线指标提供 Azure 订阅中的资源状态。 通过一组丰富的指标数据，可在命名空间和实体级别评估服务总线资源的总体运行状况。 这些统计信息非常重要，因为它们能够帮助监视服务总线的状态。 另外，指标也可帮助解决由根本原因造成的问题，而无需联系 Azure 支持。

Azure Monitor 提供了统一的用户界面，可用于监视各种 Azure 服务。 有关详细信息，请参阅 GitHub 上的[在 Microsoft Azure 中进行监视](../monitoring-and-diagnostics/monitoring-overview.md)和[通过 .NET 检索 Azure Monitor 指标](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)示例。

> [!IMPORTANT]
> 如果在 2 小时内没有与实体进行任何交互，则指标将显示“0”作为值，直到实体不再空闲为止。

## <a name="access-metrics"></a>访问指标

Azure Monitor 提供多种访问指标的方法。 你可以通过[Azure 门户](https://portal.azure.com)访问指标，或者使用 Azure Monitor API （REST 和 .net）和分析解决方案，如 Azure Monitor 日志和事件中心。 有关详细信息，请参阅[Azure Monitor 中的度量值](../azure-monitor/platform/data-platform-metrics.md)。

默认情况下，已启用指标，并且可访问最近 30 天的数据。 如需将数据保留更长一段时间，可将指标数据存档到 Azure 存储帐户。 此值是在 Azure Monitor 中的[诊断设置](../azure-monitor/platform/diagnostic-settings.md)中配置的。

## <a name="access-metrics-in-the-portal"></a>在门户中访问指标

可在 [Azure 门户](https://portal.azure.com)中监视一段时间内的指标。 以下示例演示了如何在帐户级别查看成功的请求和传入的请求：

![][1]

也可以直接通过命名空间来访问指标。 为此，请选择命名空间，然后单击 "**指标**"。 若要显示筛选到实体范围的指标，请选择该实体，然后单击 "**指标**"。

![][2]

对于支持维度的指标，必须使用所需的维度值进行筛选。

## <a name="billing"></a>计费

针对 Azure Monitor 的指标和警报按警报收费。 当安装警报并保存警报之前，应在门户上提供这些费用。 

引入指标数据的其他解决方案直接由这些解决方案进行计费。 例如，如果将指标数据存档到 Azure 存储帐户，则 Azure 存储会收费。 如果将指标数据流式传输到 Log Analytics 进行高级分析，则 Log Analytics 也会收费。

以下指标可提供服务运行状况的概述。 

> [!NOTE]
> 我们弃用了多个指标，因为它们已移动到不同的名称下。 这可能会要求你更新引用。 今后将不再支持标有“弃用的”关键字的指标。

所有指标值每隔一分钟发送到 Azure Monitor。 时间粒度定义了提供指标值的时间间隔。 所有服务总线指标支持的时间间隔都是 1 分钟。

## <a name="request-metrics"></a>请求指标

计算数据量和管理操作请求数。

| 标准名称 | 说明 |
| ------------------- | ----------------- |
| 传入请求数| 在指定的期间内向服务总线服务发出的请求数。 <br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|成功的请求数|在指定的期间内向服务总线服务发出的成功请求数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|服务器错误数|由于服务总线服务发生错误，在指定期间内未处理的请求数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|用户错误（请参阅以下子节）|由于存在用户错误，在指定期间内未处理的请求数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|限制的请求|由于使用量超标，而被限制的请求数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|

### <a name="user-errors"></a>用户错误

下面两种类型的错误被归类为用户错误：

1. 客户端错误（在 HTTP 中为 400 错误）。
2. 在处理消息时发生的错误，如 [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)。


## <a name="message-metrics"></a>消息指标

| 标准名称 | 说明 |
| ------------------- | ----------------- |
|传入消息数|在指定的期间内发送到服务总线的事件或消息数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
|传出消息数|在指定的期间内从服务总线收到的事件或消息数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|
| 消息| 队列/主题中的消息计数。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 维度：EntityName |
| ActiveMessages| 队列/主题中的活动消息的计数。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 维度：EntityName |
| 死信消息| 队列/主题中的死信消息计数。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/>维度：EntityName |
| 计划的消息| 队列/主题中计划消息的计数。 <br/><br/> 单位：计数 <br/> 聚合类型：平均  <br/> 维度：EntityName |

> [!NOTE]
> 以下指标的值为时间点值。 在该时间点之后立即使用的传入消息可能不会反映在这些指标中。 
> - 消息
> - 活动消息 
> - 死信消息 
> - 计划的消息 

## <a name="connection-metrics"></a>连接指标

| 标准名称 | 说明 |
| ------------------- | ----------------- |
|ActiveConnections|命名空间以及实体上的活动连接数。<br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 维度：EntityName|

## <a name="resource-usage-metrics"></a>资源使用情况指标

> [!NOTE] 
> 以下指标仅适用于高级层。 

| 标准名称 | 说明 |
| ------------------- | ----------------- |
|每个命名空间的 CPU 使用率|命名空间的 CPU 使用百分比。<br/><br/> 单位：百分比 <br/> 聚合类型：最大值 <br/> 维度：EntityName|
|每个命名空间的内存使用量|命名空间的内存使用百分比。<br/><br/> 单位：百分比 <br/> 聚合类型：最大值 <br/> 维度：EntityName|

## <a name="metrics-dimensions"></a>指标维度

Azure 总线服务支持对 Azure Monitor 中的指标使用以下维度。 为指标添加维度是可选的。 如果不添加维度，则指标是在命名空间级别指定的。 

|维度名称|说明|
| ------------------- | ----------------- |
|EntityName| 总线服务支持命名空间下的消息实体。|

## <a name="set-up-alerts-on-metrics"></a>针对指标设置警报

1. 在“服务总线命名空间”页面的“指标”选项卡上，选择“配置警报”。 

    ![“指标”页面 - 配置警报菜单](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. 选择 "**选择目标**" 选项，然后在 "**选择资源**" 页上执行以下操作： 
    1. 对于“按资源类型筛选”字段，选择“服务总线命名空间”。 
    2. 对于“按订阅筛选”字段，选择你的订阅。
    3. 从列表中选择“服务总线命名空间”。 
    4. 选择“完成”。 
    
        ![选择命名空间](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. 选择“添加条件”，并在“配置信号逻辑”页面上执行以下操作：
    1. 对于“信号类型”，选择“指标”。 
    2. 选择一个信号。 例如：**服务错误**。 

        ![选择“服务器错误”](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. 对于“条件”，选择“大于”。
    2. 对于“时间聚合”，选择“总计”。 
    3. 对于“阈值”，输入 **5**。 
    4. 选择“完成”。    

        ![指定条件](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. 在“创建规则”页面上，展开“定义警报详细信息”，执行以下操作：
    1. 为警报输入**名称**。 
    2. 为警报输入**说明**。
    3. 选择警报的**严重性**。 

        ![警报详细信息](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. 在“创建规则”页面上，展开“定义操作组”，选择“新建操作组”，然后在“添加操作组”页面上执行以下操作。 
    1. 为操作组输入名称。
    2. 为操作组输入短名称。 
    3. 选择订阅。 
    4. 选一个择资源组。 
    5. 在本演练中，对于“操作名称”，输入“发送电子邮件”。
    6. 对于“操作类型”，选择“电子邮件/短信/推送/语音”。 
    7. 选择“编辑详细信息”。 
    8. 在“电子邮件/短信/推送/语音”页面上，执行以下操作：
        1. 选择“电子邮件”。 
        2. 键入**电子邮件地址**。 
        3. 选择“确定”。

            ![警报详细信息](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. 在“添加操作组”页面上，选择“确定”。 
1. 在“创建规则”页面上，选择“创建警报规则”。 

    ![“创建警报规则”按钮](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>后续步骤

请参阅[Azure Monitor 概述](../monitoring-and-diagnostics/monitoring-overview.md)。

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


