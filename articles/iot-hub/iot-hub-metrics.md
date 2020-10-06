---
title: 使用指标监视 Azure IoT 中心 | Microsoft Docs
description: 如何使用 Azure IoT 中心度量值评估和监视 IoT 中心的总体运行状况。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: c448d7e5a5e0bea29063930bed3a59a0461b8cf5
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767619"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>了解 IoT 中心指标

IoT 中心度量值提供有关 Azure 订阅中的 Azure IoT 资源状态的信息。 IoT 中心度量值可帮助评估 IoT 中心服务及其所连接的设备的总体运行状况。 这些面向用户的统计信息可以帮助了解 IoT 中心的情况，并可以帮助在不需要联系 Azure 支持的情况下对问题执行根本原因分析。

默认启用度量值。 可在 Azure 门户中查看 IoT 中心度量值。

> [!NOTE]
> 可以使用 IoT 中心指标来查看有关连接到 IoT 中心的 [iot 即插即用](../iot-pnp/overview-iot-plug-and-play.md) 设备的信息。

## <a name="how-to-view-iot-hub-metrics"></a>如何查看 IoT 中心度量值

1. 创建 IoT 中心。 可以在[从设备向 IoT 中心发送遥测数据](quickstart-send-telemetry-dotnet.md)指南中找到有关如何创建 IoT 中心的说明。

2. 打开 IoT 中心的边栏选项卡。 在此处单击“度量值” 。
   
    ![显示指标选项在 IoT 中心资源页中位置的屏幕截图](./media/iot-hub-metrics/enable-metrics-1.png)

3. 在“度量值”边栏选项卡中，可查看 IoT 中心的度量值并创建度量值的自定义视图。 
   
    ![显示 IoT 中心的指标页的屏幕截图](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. 可以选择将指标数据发送到事件中心终结点或 Azure 存储帐户，方法是单击 " **诊断设置**"，然后单击 " **添加诊断设置**"。

   ![显示“诊断设置”按钮所在位置的屏幕截图](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT 中心度量值及其用法

IoT 中心提供多个度量值，帮助你大致了解中心的运行状况以及所连接的设备总数。 可以结合多个度量值的信息，更清楚地了解 IoT 中心的状态。 下表描述了每个 IoT 中心所跟踪的度量值，以及每个度量值与 IoT 中心总体状态的关联。

|指标|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|RoutingDeliveries | 路由传递尝试次数（预览版） | 计数 | 总计 |这是路由传递指标。 使用维度标识特定终结点或特定路由源的传递状态。| ResourceID、<br>Result、<br>RoutingSource、<br>EndpointType、<br>FailureReasonCategory、<br>EndpointName<br>*有关维度的更多详细信息请参阅[**此处**](#dimensions)* 。 |
|RoutingDeliveryLatency| 路由延迟（预览版） | 毫秒 | 平均值 |这是路由传递延迟指标。 使用维度标识特定终结点或特定路由源的延迟。| ResourceID、<br>RoutingSource、<br>EndpointType、<br>EndpointName<br>*有关维度的更多详细信息请参阅[**此处**](#dimensions)* 。|
|RoutingDataSizeInBytesDelivered| 路由传递数据大小（以字节为单位）（预览版）| 字节 | 总计 |IoT 中心路由到自定义终结点和内置终结点的总字节数。 使用维度标识路由到特定终结点或特定路由源的数据大小。| ResourceID、<br>RoutingSource、<br>EndpointType<br>EndpointName<br>*有关维度的更多详细信息请参阅[**此处**](#dimensions)* 。|
|d2c.telemetry.ingress.<br>allProtocol|遥测消息发送尝试次数|Count|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|无|
|d2c.telemetry.ingress.<br>success|发送的遥测消息数|Count|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|无|
|c2d.commands.egress.<br>complete.success|已完成的 C2D 消息传递次数|Count|总计|设备成功完成的云到设备消息传递次数|无|
|c2d.commands.egress.<br>abandon.success|丢弃的 C2D 消息数|Count|总计|设备丢弃的云到设备消息数|无|
|c2d.commands.egress.<br>reject.success|拒绝的 C2D 消息数|Count|总计|设备拒绝的云到设备消息数|无|
|C2DMessagesExpired|过期的 C2D 消息数（预览版）|Count|总计|过期的云到设备消息数|无|
|devices.totalDevices|设备总数（已弃用）|Count|总计|已注册到 IoT 中心的设备数目|无|
|devices.connectedDevices.<br>allProtocol|连接的设备数（已弃用） |Count|总计|已连接到 IoT 中心的设备数目|无|
|d2c.telemetry.egress.<br>success|路由：遥测消息传送次数|Count|总计|使用 IoT 中心路由将消息成功传送到所有终结点的次数。 如果某条消息已路由到多个终结点，则每成功传送一次，此值就会加 1。 如果某条消息多次路由到同一终结点，则每成功传送一次，此值就会加 1。|无|
|d2c.telemetry.egress.<br>已删除|路由：遥测消息删除次数 |Count|总计|由于终结点消亡，IoT 中心路由删除消息的次数。 此值不会统计已传送到回退路由的消息，因为已删除的消息不会传送到回退路由。|无|
|d2c.telemetry.egress.<br>已孤立|路由：遥测消息孤立次数 |Count|总计|禁用路由消息时，IoT 中心路由孤立了次数消息，因为它们与任何路由查询都不匹配。|无|
|d2c.telemetry.egress.<br>无效|路由：遥测消息不兼容|Count|总计|消息由于与终结点不兼容而无法由 IoT 中心路由传送的次数。 当 IoT 中心尝试将消息传递到终结点时，消息与终结点不兼容，传递失败并收到非暂时性错误。 不会重试无效的消息。 此值不包括重试次数。|无|
|d2c.telemetry.egress.<br>.fallback|路由：消息传送到回退路由的次数|Count|总计|IoT 中心路由将消息传送到与回退路由关联的终结点的次数。|无|
|d2c.endpoints.egress.<br>.eventHubs|路由：消息传送到事件中心的次数|Count|总计|IoT 中心路由成功将消息传送到事件中心类型的自定义终结点的次数。 这不包括指向内置终结点（事件）的消息路由。|无|
|d2c.endpoints.latency.<br>.eventHubs|路由：事件中心的消息延迟|毫秒|平均值|消息进入 IoT 中心与进入事中心类型的自定义终结点之间的平均延迟（毫秒）。 这不包括指向内置终结点（事件）的消息路由。|无|
|d2c.endpoints.egress.<br>serviceBusQueues|路由：消息传送到服务总线队列的次数|Count|总计|IoT 中心路由成功将消息传送到服务总线队列终结点的次数。|无|
|d2c.endpoints.latency.<br>serviceBusQueues|路由：服务总线队列的消息延迟|毫秒|平均值|消息进入 IoT 中心与消息进入服务总线队列终结点之间的平均延迟（毫秒）。|无|
|d2c.endpoints.egress.<br>serviceBusTopics|路由：消息传送到服务总线主题的次数|Count|总计|IoT 中心路由成功将消息传送到服务总线主题终结点的次数。|无|
|d2c.endpoints.latency.<br>serviceBusTopics|路由：服务总线主题的消息延迟|毫秒|平均值|消息进入 IoT 中心与消息进入服务总线主题终结点之间的平均延迟（毫秒）。|无|
|d2c.endpoints.egress.<br>builtIn.events|路由：消息传送到消息/事件的次数|Count|总计|IoT 中心路由成功将消息传送到内置终结点（消息/事件）以及回退路由的次数。|无|
|d2c.endpoints.latency.<br>builtIn.events|路由：消息/事件的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入内置终结点（消息/事件）以及回退路由之间的平均延迟（毫秒）。|无|
|d2c.endpoints.egress.<br>storage|路由：消息传送到存储的次数|Count|总计|IoT 中心路由成功将消息传送到存储终结点的次数。|无|
|d2c.endpoints.latency.<br>storage|路由：存储的消息延迟|毫秒|平均值|消息进入 IoT 中心与消息进入存储终结点之间的平均延迟（毫秒）。|无|
|d2c.endpoints.egress.<br>storage.bytes|路由：传送到存储的数据量|字节|总计|IoT 中心路由传送到存储终结点的数据量（字节）。|无|
|d2c.endpoints.egress.<br>storage.blobs|路由：将 Blob 传送到存储的次数|Count|总计|IoT 中心路由将 Blob 传送到存储终结点的次数。|无|
|EventGridDeliveries|事件网格传递次数（预览版）|Count|总计|发布到事件网格的 IoT 中心事件的数量。 使用 Result 维度表示成功和失败请求的数量。 EventType 维度显示事件的类型 (https://aka.ms/ioteventgrid) 。|ResourceId、<br/>Result、<br/>EventType|
|EventGridLatency|事件网格延迟（预览）|毫秒|平均值|从生成 IoT 中心事件到将事件发布到事件网格的平均延迟（毫秒）。 此数值是所有事件类型的平均。 若要查看特定事件类型的延迟，请使用 EventType 维度。|ResourceId、<br/>EventType|
|d2c.twin.read.success|设备的成功孪生读取数|Count|总计|由设备发起的所有成功孪生读取的计数。|无|
|d2c.twin.read.failure|设备的失败孪生读取数|Count|总计|由设备发起的所有失败孪生读取的计数。|无|
|d2c.twin.read.size|设备的孪生读取的响应大小|字节|平均值|由设备发起的所有成功的孪生读取的数量。|无|
|d2c.twin.update.success|设备的成功孪生更新数|Count|总计|由设备发起的所有成功的孪生更新的计数。|无|
|d2c.twin.update.failure|设备的失败孪生更新数|Count|总计|由设备发起的所有失败的孪生更新的计数。|无|
|d2c.twin.update.size|设备的孪生更新的大小|字节|平均值|由设备发起的所有成功的孪生更新的总大小。|无|
|c2d.methods.success|成功的直接方法调用数|Count|总计|所有成功的直接方法调用的计数。|无|
|c2d.methods.failure|失败的直接方法调用数|Count|总计|所有失败直接方法调用的计数。|无|
|c2d.methods.requestSize|直接方法调用的请求大小|字节|平均值|所有成功的直接方法请求计数。|无|
|c2d.methods.responseSize|直接方法调用的响应大小|字节|平均值|所有成功的直接方法响应计数。|无|
|c2d.twin.read.success|后端的成功孪生读取数|Count|总计|由后端发起的所有成功孪生读取的计数。|无|
|c2d.twin.read.failure|后端的失败孪生读取数|Count|总计|由后端发起的所有失败孪生读取的计数。|无|
|c2d.twin.read.size|后端的孪生读取的响应大小|字节|平均值|由后端发起的所有成功孪生读取的计数。|无|
|c2d.twin.update.success|后端的成功孪生更新数|Count|总计|由后端发起的所有成功孪生更新的计数。|无|
|c2d.twin.update.failure|后端的失败孪生更新数|Count|总计|由后端发起的所有失败孪生更新的计数。|无|
|c2d.twin.update.size|后端的失败孪生更新大小|字节|平均值|由后端发起的所有成功孪生更新的总大小。|无|
|twinQueries.success|成功的孪生查询数|Count|总计|所有成功孪生查询的计数。|无|
|twinQueries.failure|失败的孪生查询数|Count|总计|所有失败孪生查询的计数。|无|
|twinQueries.resultSize|孪生查询结果大小|字节|平均值|所有成功的孪生查询结果的总大小。|无|
|jobs.createTwinUpdateJob.<br>success|孪生更新作业创建成功数|Count|总计|孪生更新作业创建成功的所有次数。|无|
|jobs.createTwinUpdateJob.<br>失败|孪生更新作业创建失败数|Count|总计|孪生更新作业创建失败的所有次数。|无|
|jobs.createDirectMethodJob.<br>success|方法调用作业的创建成功数|Count|总计|直接方法调用作业创建成功的所有次数。|无|
|jobs.createDirectMethodJob.<br>失败|方法调用作业的创建失败数|Count|总计|直接方法调用作业创建失败的所有次数。|无|
|jobs.listJobs.success|对列出作业的成功调用数|Count|总计|对列出作业的所有成功调用的计数。|无|
|jobs.listJobs.failure|对列出作业的失败调用数|Count|总计|对列出作业的所有失败调用的计数。|无|
|jobs.cancelJob.success|成功的作业取消数|Count|总计|用来取消作业的调用成功的次数。|无|
|jobs.cancelJob.failure|失败的作业取消数|Count|总计|用来取消作业的调用失败的次数。|无|
|jobs.queryJobs.success|成功的作业查询数|Count|总计|对查询作业的所有成功调用的计数。|无|
|jobs.queryJobs.failure|失败的作业查询数|Count|总计|对查询作业的所有失败调用的计数。|无|
|jobs.completed|已完成的作业|Count|总计|所有已完成的作业的计数。|无|
|jobs.failed|失败的作业数|Count|总计|所有失败的作业的计数。|无|
|d2c.telemetry.ingress.<br>sendThrottle|限制错误数|Count|总计|由于设备吞吐量限制而导致的限制错误数|无|
|dailyMessageQuotaUsed|已使用的消息总数|Count|平均值|今天使用的消息总数。 这是累积值，每日 00:00 UTC 重置为零。|无|
|deviceDataUsage|设备数据用量总计|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|deviceDataUsageV2|设备数据用量总计（预览）|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|totalDeviceCount|设备总数（预览）|Count|平均值|已注册到 IoT 中心的设备数目|无|
|connectedDeviceCount|连接设备数（预览）|Count|平均值|已连接到 IoT 中心的设备数目|无|
|配置|配置指标|Count|总计|在一组目标设备上为设备配置和 IoT Edge 部署执行的 CRUD 操作总数。 这还包括由于这些配置而修改设备孪生或模块孪生的操作的数量。|无|

### <a name="dimensions"></a>维度
维度有助于标识有关指标的更多详细信息。 某些路由指标提供了每个终结点的信息。 下表列出了这些维度的可能值。

|维度|值|
|---|---|
|ResourceID|IoT 中心资源|
|结果|success<br>失败|
|RoutingSource|设备消息<br>孪生更改事件<br>设备生命周期事件|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>builtIn<br>blobStorage|
|FailureReasonCategory|无效<br>已删除<br>已孤立<br>Null|
|EndpointName|终结点名称|

## <a name="next-steps"></a>后续步骤

现已大致了解了 IoT 中心度量值，请单击此链接，深入了解如何管理 Azure IoT 中心：

* [设置诊断日志](iot-hub-monitor-resource-health.md)

* [了解消息路由疑难解答](troubleshoot-message-routing.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)

* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
