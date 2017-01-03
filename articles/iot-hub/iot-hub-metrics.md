---
title: "IoT 中心诊断度量值"
description: "概述了 Azure IoT 中心内的度量值，用户可以通过这些指标评估其资源的总体运行状况"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: e2739f9f47495c4502af3aff95d2d3f3975a881b


---
# <a name="introduction-to-diagnostic-metrics"></a>诊断度量值简介
诊断度量值提供有关 Azure 订阅中 Azure 资源状态的更清晰的数据。 你可以使用度量值评估服务以及连接到服务的设备的总体运行状况。 面向用户的统计信息非常重要，因为它们可以帮助你了解其 IoT 中心的情况，帮助你在不联系 Azure 支持人员的情况下解决根本问题。

可以从 Azure 门户启用诊断度量值。

## <a name="how-to-enable-diagnostic-metrics"></a>如何启用诊断度量值
1. 创建 IoT 中心。 有关如何创建 IoT 中心的说明，请参阅[入门][lnk-get-started]指南。
2. 打开 IoT 中心的边栏选项卡。 在其中单击“**诊断**”。
   
    ![][1]
3. 将状态设置为“开”并选择用于存储诊断数据的 Azure 存储帐户，从而配置诊断。 检查“**度量**”，然后按“**保存**”。 请注意，必须提前创建 Azure 存储帐户，并且需要单独为存储付费。 你还可以选择将诊断数据发送到事件中心终结点。
   
    ![][2]
4. 设置诊断后，返回到“**概述**”IoT 中心边栏选项卡。 边栏选项卡的“**监视**”部分中填充了度量值信息。 单击图表打开度量值窗格，可以在此处查看 IoT 中心的度量值信息摘要。 可以编辑图表中显示的度量值的选择并配置基于度量值的警报。
   
    ![][3]

## <a name="metrics-and-how-to-use-them"></a>度量值及其用法
IoT 中心提供了多个度量值，使你可以大致了解中心的运行状况以及已连接的设备总数。 可以结合多个度量值的信息，更清楚地了解 IoT 中心的状态。 下表描述了每个 IoT 中心所跟踪的度量值，以及每个度量值与 IoT 中心总体状态的关联。

| 度量值 | 度量值说明 | 度量值用途 |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |所有设备上发送的消息数目 |有关消息发送操作的概述数据 |
| d2c.telemetry.ingress.success |成功传入 IoT 中心的消息总数 |成功传入 IoT 中心的消息的概述 |
| c2d.commands.egress.complete.success |接收设备在所有设备上完成的所有命令消息计数 |结合有关放弃或拒绝的度量值，概述了“云到设备”命令的总体成功率 |
| c2d.commands.egress.abandon.success |接收设备在所有设备上成功放弃的消息总数 |如果消息被放弃的频率超出预期，则突显潜在问题 |
| c2d.commands.egress.reject.success |接收设备在所有设备上成功拒绝的消息总数 |如果消息被拒绝的频率超出预期，则突显潜在问题 |
| devices.totalDevices |向 IoT 中心注册的设备的平均数目、最小数目和最大数目 |已注册到 IoT 中心的设备的数目 |
| devices.connectedDevices.allProtocol |同时连接的设备的平均数目、最小数目和最大数目 |已连接到 IoT 中心的设备数概述 |

## <a name="next-steps"></a>后续步骤
现在你已大概了解了诊断度量值，接下来请单击以下链接来了解有关管理 Azure IoT 中心的详细信息：

* [操作监视][lnk-monitor]

若要进一步探索 IoT 中心的功能，请参阅：

* [开发人员指南][lnk-devguide]
* [使用 IoT 网关 SDK 模拟设备][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


