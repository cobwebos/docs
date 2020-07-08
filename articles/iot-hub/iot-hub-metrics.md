---
title: 使用指标监视 Azure IoT 中心 | Microsoft Docs
description: 如何使用 Azure IoT 中心度量值评估和监视 IoT 中心的总体运行状况。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 808320f89c4dbeca835fc5a710ea1566199f6884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791837"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>了解 IoT 中心指标

IoT 中心指标为你介绍 Azure 订阅中 Azure IoT 资源的状态。 IoT 中心指标可帮助你评估 IoT 中心服务及其所连接的设备的总体运行状况。 这些面向用户的统计信息可帮助你查看 IoT 中心正在进行的操作，并帮助对问题执行根本原因分析，而无需联系 Azure 支持部门。

默认启用度量值。 可在 Azure 门户中查看 IoT 中心度量值。

> [!NOTE]
> 可以使用 IoT 中心指标来查看有关连接到 IoT 中心的 IoT 即插即用设备的信息。 IoT 即插即用设备是[iot 即插即用公共预览版](../iot-pnp/overview-iot-plug-and-play.md)的一部分。

## <a name="how-to-view-iot-hub-metrics"></a>如何查看 IoT 中心度量值

1. 创建 IoT 中心。 可以在[从设备向 IoT 中心发送遥测数据](quickstart-send-telemetry-dotnet.md)指南中找到有关如何创建 IoT 中心的说明。

2. 打开 IoT 中心的边栏选项卡。 在此处单击“指标”****。
   
    ![显示指标选项在 IoT 中心资源页中位置的屏幕截图](./media/iot-hub-metrics/enable-metrics-1.png)

3. 在“度量值”边栏选项卡中，可查看 IoT 中心的度量值并创建度量值的自定义视图。 
   
    ![显示 IoT 中心的指标页的屏幕截图](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. 可以选择将指标数据发送到事件中心终结点或 Azure 存储帐户，方法是单击 "**诊断设置**"，然后单击 "**添加诊断设置**"。

   ![显示“诊断设置”按钮所在位置的屏幕截图](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT 中心度量值及其用法

IoT 中心提供了多个指标，使你可以大致了解中心的运行状况以及已连接的设备总数。 可以结合多个度量值的信息，更清楚地了解 IoT 中心的状态。 下表描述了每个 IoT 中心所跟踪的度量值，以及每个度量值与 IoT 中心总体状态的关联。

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RoutingDeliveries | 路由传递尝试次数（预览） | 计数 | 总计 |这是路由传递指标。 使用维度标识特定终结点或特定路由源的传递状态。| ResourceID<br>Result、<br>RoutingSource,<br>EndpointType<br>FailureReasonCategory,<br>EndpointName<br>有关*维度的详细[**here**](#dimensions)信息*，请参阅此处。 |
|RoutingDeliveryLatency| 路由延迟（预览版） | 毫秒 | 平均值 |这是路由传递延迟指标。 使用维度来确定特定终结点或特定路由源的延迟。| ResourceID<br>RoutingSource,<br>EndpointType<br>EndpointName<br>有关*维度的详细[**here**](#dimensions)信息*，请参阅此处。|
|RoutingDataSizeInBytesDelivered| 路由传递数据大小（以字节为单位）（预览版）| 字节 | 总计 |IoT 中心路由到自定义终结点和内置终结点的总字节数。 使用维度标识路由到特定终结点或特定路由源的数据大小。| ResourceID<br>RoutingSource,<br>EndpointType<br>EndpointName<br>有关*维度的详细[**here**](#dimensions)信息*，请参阅此处。|
|d2c。<br>allProtocol|遥测消息发送尝试次数|计数|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|None|
|d2c。<br>success|已发送的遥测消息数|Count|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|None|
|c2d。<br>完成。成功|已完成的 C2D 消息传递数|计数|总计|设备成功完成的云到设备消息传递次数|None|
|c2d。<br>放弃。成功|已弃用的 C2D 消息数|计数|总计|设备丢弃的云到设备消息数|None|
|c2d。<br>拒绝。成功|已拒绝的 C2D 消息数|计数|总计|设备拒绝的云到设备消息数|无|
|C2DMessagesExpired|已过期的 C2D 消息(预览)|计数|总计|过期的云到设备消息数|无|
|devices.totalDevices|（已弃用）设备总数|Count|总计|已注册到 IoT 中心的设备数目|None|
|connectedDevices。<br>allProtocol|（已弃用）连接设备数 |计数|总计|已连接到 IoT 中心的设备数目|None|
|d2c。<br>success|路由：已发送的遥测消息数|计数|总计|使用 IoT 中心路由将消息成功传送到所有终结点的次数。 如果某条消息已路由到多个终结点，则每成功传送一次，此值就会加 1。 如果某条消息多次路由到同一终结点，则每成功传送一次，此值就会加 1。|无|
|d2c。<br>删除|路由：已删除的遥测消息数 |计数|总计|由于终结点消亡，IoT 中心路由删除消息的次数。 此值不会统计已传送到回退路由的消息，因为已删除的消息不会传送到回退路由。|None|
|d2c。<br>孤立|路由：已孤立的遥测消息数 |计数|总计|由于禁用了回退路由，因此 IoT 中心路由将消息孤立的次数，因为这些消息不匹配任何路由查询。|None|
|d2c。<br>无效|路由：不兼容的遥测消息数|计数|总计|消息由于与终结点不兼容而无法由 IoT 中心路由传送的次数。 当 Iot 中心尝试将消息传递到终结点时，消息与终结点不兼容，并且失败并出现非暂时性错误。 无效的消息不会重试。 此值不包括重试次数。|None|
|d2c。<br>fallback|路由：发送到回退的消息数|计数|总计|IoT 中心路由将消息传送到与回退路由关联的终结点的次数。|None|
|d2c。<br>eventHubs|路由：已发送到事件中心的消息数|计数|总计|IoT 中心路由成功地将消息传送到事件中心类型的自定义终结点的次数。 这不包括消息路由到内置终结点（事件）。|None|
|d2c。<br>eventHubs|路由：事件中心的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入事件中心类型的自定义终结点之间的平均延迟（毫秒）。 这不包括消息路由到内置终结点（事件）。|无|
|d2c。<br>serviceBusQueues|路由：已发送到服务总线队列的消息数|计数|总计|IoT 中心路由成功将消息传送到服务总线队列终结点的次数。|None|
|d2c。<br>serviceBusQueues|路由：服务总线队列的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入服务总线队列终结点之间的平均延迟（毫秒）。|None|
|d2c。<br>serviceBusTopics|路由：发送到服务总线主题的消息数|计数|总计|IoT 中心路由成功将消息传送到服务总线主题终结点的次数。|无|
|d2c。<br>serviceBusTopics|路由：服务总线主题的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入服务总线主题终结点之间的平均延迟（毫秒）。|None|
|d2c。<br>内置事件|路由：发送到消息/事件的消息数|计数|总计|IoT 中心路由成功地将消息传递到内置终结点（消息/事件）和回退路由的次数。|None|
|d2c。<br>内置事件|路由：消息/事件的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入内置终结点（消息/事件）和回退路由之间的平均延迟（毫秒）。|None|
|d2c。<br>存储|路由：发送到存储器的消息数|Count|总计|IoT 中心路由成功将消息传送到存储终结点的次数。|无|
|d2c。<br>存储|路由：存储器的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入存储终结点之间的平均延迟（毫秒）。|None|
|d2c。<br>存储字节数|路由：发送到存储器的数据数|字节|总计|IoT 中心路由传送到存储终结点的数据量（字节）。|None|
|d2c。<br>存储 blob|路由：发送到存储器的 blob 数|计数|总计|IoT 中心路由将 Blob 传送到存储终结点的次数。|None|
|EventGridDeliveries|事件网格传递数(预览)|Count|总计|发布到事件网格的 IoT 中心事件的数量。 使用 Result 维度表示成功和失败请求的数量。 EventType 维度显示事件的类型 (https://aka.ms/ioteventgrid) 。|ResourceId、<br/>Result、<br/>EventType|
|EventGridLatency|事件网格延迟(预览)|毫秒|平均值|从生成 IoT 中心事件到将事件发布到事件网格的平均延迟（毫秒）。 此数值是所有事件类型的平均。 若要查看特定事件类型的延迟，请使用 EventType 维度。|ResourceId、<br/>EventType|
|d2c.twin.read.success|设备的成功克隆读取数|计数|总计|由设备发起的所有成功孪生读取的计数。|None|
|d2c.twin.read.failure|设备的失败克隆读取数|计数|总计|由设备发起的所有失败孪生读取的计数。|None|
|d2c.twin.read.size|设备的克隆读取的响应大小|字节|平均值|设备启动的所有成功的克隆读取的数目。|None|
|d2c.twin.update.success|设备的成功克隆更新数|计数|总计|由设备发起的所有成功的孪生更新的计数。|None|
|d2c.twin.update.failure|设备的失败克隆更新数|计数|总计|由设备发起的所有失败的孪生更新的计数。|None|
|d2c.twin.update.size|设备的克隆更新的大小|字节|平均值|设备启动的所有成功的克隆更新的总大小。|None|
|c2d.methods.success|成功的直接方法调用数|计数|总计|所有成功的直接方法调用的计数。|无|
|c2d.methods.failure|失败的直接方法调用数|计数|总计|所有失败直接方法调用的计数。|None|
|c2d.methods.requestSize|直接方法调用的请求大小|字节|平均值|所有成功的直接方法请求的计数。|None|
|c2d.methods.responseSize|直接方法调用的响应大小|字节|平均值|所有成功的直接方法响应的计数。|None|
|c2d.twin.read.success|后端的成功克隆读取数|计数|总计|由后端发起的所有成功孪生读取的计数。|None|
|c2d.twin.read.failure|后端的失败克隆读取数|计数|总计|由后端发起的所有失败孪生读取的计数。|None|
|c2d.twin.read.size|后端的克隆读取的响应大小|字节|平均值|由后端发起的所有成功孪生读取的计数。|None|
|c2d.twin.update.success|后端的成功克隆更新数|计数|总计|由后端发起的所有成功孪生更新的计数。|None|
|c2d.twin.update.failure|后端的失败克隆更新数|计数|总计|由后端发起的所有失败孪生更新的计数。|None|
|c2d.twin.update.size|后端的克隆更新的大小|字节|平均值|由后端发起的所有成功的克隆更新的总大小。|None|
|twinQueries.success|成功的克隆查询|Count|总计|所有成功孪生查询的计数。|None|
|twinQueries.failure|失败的克隆查询|计数|总计|所有失败孪生查询的计数。|None|
|twinQueries.resultSize|克隆查询结果大小|字节|平均值|所有成功的克隆查询的结果大小总计。|None|
|createTwinUpdateJob。<br>success|克隆更新作业创建成功数|计数|总计|孪生更新作业创建成功的所有次数。|None|
|createTwinUpdateJob。<br>失败|克隆更新作业创建失败数|Count|总计|孪生更新作业创建失败的所有次数。|无|
|createDirectMethodJob。<br>success|方法调用作业的创建成功数|计数|总计|直接方法调用作业创建成功的所有次数。|无|
|createDirectMethodJob。<br>失败|方法调用作业的创建失败数|计数|总计|直接方法调用作业创建失败的所有次数。|None|
|jobs.listJobs.success|对列出作业的成功调用数|计数|总计|对列出作业的所有成功调用的计数。|无|
|jobs.listJobs.failure|对列出作业的失败调用数|计数|总计|对列出作业的所有失败调用的计数。|None|
|jobs.cancelJob.success|成功的作业取消数|计数|总计|用来取消作业的调用成功的次数。|None|
|jobs.cancelJob.failure|失败的作业取消数|计数|总计|用来取消作业的调用失败的次数。|None|
|jobs.queryJobs.success|成功的作业查询数|计数|总计|对查询作业的所有成功调用的计数。|None|
|jobs.queryJobs.failure|失败的作业查询数|计数|总计|对查询作业的所有失败调用的计数。|None|
|jobs.completed|已完成的作业|计数|总计|所有已完成的作业的计数。|None|
|jobs.failed|失败的作业数|计数|总计|所有失败的作业的计数。|None|
|d2c。<br>sendThrottle|限制错误数|计数|总计|由于设备吞吐量限制而导致的限制错误数|None|
|dailyMessageQuotaUsed|已使用的消息总数|计数|平均值|今天使用的消息总数。 这是累积值，每日 00:00 UTC 重置为零。|None|
|deviceDataUsage|设备数据使用总量|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|deviceDataUsageV2|设备数据使用总量（预览）|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|totalDeviceCount|设备总数（预览）|Count|平均值|已注册到 IoT 中心的设备数目|None|
|connectedDeviceCount|已连接设备数（预览）|计数|平均值|已连接到 IoT 中心的设备数目|无|
|配置|配置指标|计数|总计|在一组目标设备上为设备配置和 IoT Edge 部署执行的 CRUD 操作总数。 这还包括由于这些配置而修改设备克隆或模块克隆的操作的数目。|None|

### <a name="dimensions"></a>维度
维度有助于标识有关指标的更多详细信息。 某些路由度量提供了每个终结点的信息。 下表列出了这些维度的可能值。

|维度|值|
|---|---|
|ResourceID|IoT 中心资源|
|结果|success<br>失败|
|RoutingSource|设备消息<br>克隆更改事件<br>设备生命周期事件|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>l<br>blobStorage|
|FailureReasonCategory|无效<br>删除<br>孤立<br>null|
|EndpointName|终结点名称|

## <a name="next-steps"></a>后续步骤

现已大致了解了 IoT 中心度量值，请单击此链接，深入了解如何管理 Azure IoT 中心：

* [设置诊断日志](iot-hub-monitor-resource-health.md)

* [了解如何排查消息路由问题](troubleshoot-message-routing.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)

* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
