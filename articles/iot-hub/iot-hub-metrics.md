---
title: 使用指标监视 Azure IoT 中心 | Microsoft Docs
description: 如何使用 Azure IoT 中心度量值评估和监视 IoT 中心的总体运行状况。
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: b1c9854d794c88c28c82a4b6e40c81a29552223a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984041"
---
# <a name="understand-iot-hub-metrics"></a>了解 IoT 中心指标
IoT 中心度量值提供更棒的数据，清晰显示 Azure 订阅中的 Azure IoT 资源状态。 通过 IoT 中心度量值，可评估 IoT 中心服务及其所连接的设备的总体运行状况。 面向用户的统计信息非常重要，因为它们可以帮助了解 IoT 中心的情况，并可以帮助在不联系 Azure 支持人员的情况下解决根本问题。

默认启用度量值。 可在 Azure 门户中查看 IoT 中心度量值。

## <a name="how-to-view-iot-hub-metrics"></a>如何查看 IoT 中心度量值
1. 创建 IoT 中心。 有关如何创建 IoT 中心的说明，请参阅[入门][lnk-get-started]指南。
2. 打开 IoT 中心的边栏选项卡。 在此处单击“指标”。
   
    ![][1]
3. 在“度量值”边栏选项卡中，可查看 IoT 中心的度量值并创建度量值的自定义视图。 单击“诊断设置”，即可选择将指标数据发送到事件中心终结点或 Azure 存储帐户。
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT 中心度量值及其用法
IoT 中心提供了多个指标，使你可以大致了解中心的运行状况以及已连接的设备总数。 可以结合多个度量值的信息，更清楚地了解 IoT 中心的状态。 下表描述了每个 IoT 中心所跟踪的度量值，以及每个度量值与 IoT 中心总体状态的关联。

|指标|指标显示名称|单位|聚合类型|Description|维度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遥测消息发送尝试次数|Count|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|d2c.telemetry.ingress.success|已发送的遥测消息数|Count|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|无维度|
|c2d.commands.egress.complete.success|完成的命令数|Count|总计|设备已成功完成的云到设备命令的数目|无维度|
|c2d.commands.egress.abandon.success|放弃的命令数|Count|总计|设备放弃的云到设备命令的数目|无维度|
|c2d.commands.egress.reject.success|拒绝的命令数|Count|总计|设备拒绝的云到设备命令的数目|无维度|
|devices.totalDevices|（已弃用）设备总数|Count|总计|已注册到 IoT 中心的设备数目|无维度|
|devices.connectedDevices.allProtocol|（已弃用）连接设备数 |Count|总计|已连接到 IoT 中心的设备数目|无维度|
|d2c.telemetry.egress.success|路由：已发送的遥测消息数|Count|总计|已使用 IoT 中心路由将消息成功发送到所有终结点的次数。 如果消息路由到多个终结点，每次成功发送后，此值将逐一增加。 如果消息多次路由到同一终结点，每次成功发送后，此值将逐一增加。|无维度|
|d2c.telemetry.egress.dropped|路由：已删除的遥测消息数 |Count|总计|由于死终结点，IoT 中心路由删除消息的次数。 此值不会计算已发送到回退路由的消息，因为已删除的消息未发送到该位置。|无维度|
|d2c.telemetry.egress.orphaned|路由：已孤立的遥测消息数 |Count|总计|由于消息不匹配任何传递规则（包括回退规则），IoT 中心路由孤立消息的次数。 |无维度|
|d2c.telemetry.egress.invalid|路由：不兼容的遥测消息数|Count|总计|由于与终结点不兼容，IoT 中心路由未能发送消息的次数。 此值不包括重试次数。|无维度|
|d2c.telemetry.egress.fallback|路由：发送到回退的消息数|Count|总计|IoT 中心路由将消息发送到与回退路由相关联的终结点的次数。|无维度|
|d2c.endpoints.egress.eventHubs|路由：已发送到事件中心的消息数|Count|总计|IoT 中心路由成功将消息发送到事件中心终结点的次数。|无维度|
|d2c.endpoints.latency.eventHubs|路由：事件中心的消息延迟|毫秒|平均值|消息进入 IoT 中心与消息进入事件中心终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.serviceBusQueues|路由：已发送到服务总线队列的消息数|Count|总计|IoT 中心路由成功将消息发送到服务总线队列终结点的次数。|无维度|
|d2c.endpoints.latency.serviceBusQueues|路由：服务总线队列的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线队列终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.serviceBusTopics|路由：发送到服务总线主题的消息数|Count|总计|IoT 中心路由将消息成功发送到服务总线主题终结点的次数。|无维度|
|d2c.endpoints.latency.serviceBusTopics|路由：服务总线主题的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入服务总线主题终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.builtIn.events|路由：发送到消息/事件的消息数|Count|总计|IoT 中心路由成功将消息发送到内置终结点（消息/事件）的次数。|无维度|
|d2c.endpoints.latency.builtIn.events|路由：消息/事件的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入内置终结点（消息/事件）之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.storage|路由：发送到存储器的消息数|Count|总计|IoT 中心路由成功将消息发送到存储器终结点的次数。|无维度|
|d2c.endpoints.latency.storage|路由：存储器的消息延迟|毫秒|平均值|消息进入 IoT 中心与遥测消息进入存储器终结点之间的平均延迟时间（毫秒）。|无维度|
|d2c.endpoints.egress.storage.bytes|路由：发送到存储器的数据数|字节|总计|IoT 中心路由发送到存储器终结点的数据量（字节）。|无维度|
|d2c.endpoints.egress.storage.blobs|路由：发送到存储器的 blob 数|Count|总计|IoT 中心路由将 blob 发送到存储器终结点的次数。|无维度|
|d2c.twin.read.success|设备的成功克隆读取数|Count|总计|由设备发起的所有成功的克隆读取的计数。|无维度|
|d2c.twin.read.failure|设备的失败克隆读取数|Count|总计|由设备发起的所有失败的克隆读取的计数。|无维度|
|d2c.twin.read.size|设备的克隆读取的响应大小|字节|平均值|由设备发起的所有成功的克隆读取的平均大小、最小大小和最大大小。|无维度|
|d2c.twin.update.success|设备的成功克隆更新数|Count|总计|由设备发起的所有成功的克隆更新的计数。|无维度|
|d2c.twin.update.failure|设备的失败克隆更新数|Count|总计|由设备发起的所有失败的克隆更新的计数。|无维度|
|d2c.twin.update.size|设备的克隆更新的大小|字节|平均值|由设备发起的所有成功的克隆更新的平均大小、最小大小和最大大小。|无维度|
|c2d.methods.success|成功的直接方法调用数|Count|总计|所有成功的直接方法调用的计数。|无维度|
|c2d.methods.failure|失败的直接方法调用数|Count|总计|所有失败的直接方法调用的计数。|无维度|
|c2d.methods.requestSize|直接方法调用的请求大小|字节|平均值|所有成功的直接方法请求的平均大小、最小大小和最大大小。|无维度|
|c2d.methods.responseSize|直接方法调用的响应大小|字节|平均值|所有成功的直接方法响应的平均大小、最小大小和最大大小。|无维度|
|c2d.twin.read.success|后端的成功克隆读取数|Count|总计|由后端发起的所有成功的克隆读取的计数。|无维度|
|c2d.twin.read.failure|后端的失败克隆读取数|Count|总计|由后端发起的所有失败的克隆读取的计数。|无维度|
|c2d.twin.read.size|后端的克隆读取的响应大小|字节|平均值|由后端发起的所有成功的克隆读取的平均大小、最小大小和最大大小。|无维度|
|c2d.twin.update.success|后端的成功克隆更新数|Count|总计|由后端发起的所有成功的克隆更新的计数。|无维度|
|c2d.twin.update.failure|后端的失败克隆更新数|Count|总计|由后端发起的所有失败的克隆更新的计数。|无维度|
|c2d.twin.update.size|后端的克隆更新的大小|字节|平均值|由后端发起的所有成功的克隆更新的平均大小、最小大小和最大大小。|无维度|
|twinQueries.success|成功的克隆查询|Count|总计|所有成功的克隆查询的计数。|无维度|
|twinQueries.failure|失败的克隆查询|Count|总计|所有失败的克隆查询的计数。|无维度|
|twinQueries.resultSize|克隆查询结果大小|字节|平均值|所有成功的克隆查询的结果大小的平均值、最小值和最大值。|无维度|
|jobs.createTwinUpdateJob.success|克隆更新作业创建成功数|Count|总计|克隆更新作业创建成功的所有次数。|无维度|
|jobs.createTwinUpdateJob.failure|克隆更新作业创建失败数|Count|总计|克隆更新作业创建失败的所有次数。|无维度|
|jobs.createDirectMethodJob.success|方法调用作业的创建成功数|Count|总计|直接方法调用作业创建成功的所有次数。|无维度|
|jobs.createDirectMethodJob.failure|方法调用作业的创建失败数|Count|总计|直接方法调用作业创建失败的所有次数。|无维度|
|jobs.listJobs.success|对列出作业的成功调用数|Count|总计|对列出作业的所有成功调用的计数。|无维度|
|jobs.listJobs.failure|对列出作业的失败调用数|Count|总计|对列出作业的所有失败调用的计数。|无维度|
|jobs.cancelJob.success|成功的作业取消数|Count|总计|用来取消作业的调用成功的次数。|无维度|
|jobs.cancelJob.failure|失败的作业取消数|Count|总计|用来取消作业的调用失败的次数。|无维度|
|jobs.queryJobs.success|成功的作业查询数|Count|总计|对查询作业的所有成功调用的计数。|无维度|
|jobs.queryJobs.failure|失败的作业查询数|Count|总计|对查询作业的所有失败调用的计数。|无维度|
|jobs.completed|已完成的作业|Count|总计|所有已完成的作业的计数。|无维度|
|jobs.failed|失败的作业数|Count|总计|所有失败的作业的计数。|无维度|
|d2c.telemetry.ingress.sendThrottle|限制错误数|Count|总计|由于设备吞吐量限制而导致的限制错误数|无维度|
|dailyMessageQuotaUsed|已使用的消息总数|Count|平均值|今天使用的消息总数。 这是累积值，每日 00:00 UTC 重置为零。|无维度|
|deviceDataUsage|devicedata 使用总量（已弃用）|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无维度|
|deviceDataUsageV2|设备数据使用总量（预览）|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无维度|
|totalDeviceCount|设备总数（预览）|Count|平均值|已注册到 IoT 中心的设备数目|无维度|
|connectedDeviceCount|已连接设备数（预览）|Count|平均值|已连接到 IoT 中心的设备数目|无维度|
|配置|配置指标|Count|总计|配置操作的指标|无维度|

## <a name="next-steps"></a>后续步骤
现已大致了解了 IoT 中心度量值，请单击此链接，深入了解如何管理 Azure IoT 中心：

* [操作监视][lnk-monitor]

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南][lnk-devguide]
* [使用 Azure IoT Edge 将 AI 部署到边缘设备][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
