---
title: "诊断并解决问题 | Microsoft Docs"
description: "本教程介绍如何在时序见解环境中诊断并解决问题"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 46d58e94f3fede3f7f19de8d97e23ac6910bc12a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017

---
# <a name="diagnose-and-solve-problems"></a>诊断并解决问题

## <a name="i-do-not-see-my-data"></a>我看不到我的数据
以下是可能在[时序见解门户](https://insights.timeseries.azure.com)中的环境中看不到数据的一些原因。

### <a name="does-your-event-source-have-data-in-json-format"></a>事件源是否具有 JSON 格式的数据？
Azure 时序见解目前仅支持 JSON 数据。 有关 JSON 示例，请在[此处](time-series-insights-send-events.md#supported-json-shapes)参阅“支持的 JSON 形状”一节。

### <a name="when-registering-your-event-source-did-you-provide-the-key-with-required-permissions"></a>注册事件源时，是否为密钥提供了所需权限？
1. 对于 IoTHub，需为密钥提供“服务连接”权限。

   ![IotHub 服务连接权限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   如上述图像中所示，“iothubowner”或“服务”策略均可以使用，因为两者都具有“服务连接”权限。
2. 对于 EventHub，需为密钥提供“侦听”权限。

   ![事件中心侦听权限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   如上述图像中所示，“读取”或“管理”策略均可以使用，因为两者都具有“读取”权限

### <a name="are-you-sure-that-the-consumer-group-provided-is-exclusive-to-time-series-insights"></a>确定所提供的使用者组是时序见解独有的吗？
对于 IoTHub 或 EventHub，我们要求在注册期间指定应在读取数据时使用的使用者组。 绝对不能共享此使用者组。 如果共享，基础事件中心将自动随机断开一个读取器的连接。

## <a name="i-see-my-data-but-there-is-a-lag"></a>我能看到数据，但存在延迟的情况
以下是可能在[时序见解门户](https://insights.timeseries.azure.com)中的环境中看到部分数据的一些原因。

### <a name="your-environment-might-be-getting-throttled"></a>你的环境可能受到了限制
基于环境 SKU 种类和容量将强制对环境进行限制。 环境中的所有事件源都共享此容量。 如果事件中心 / IoT 中心事件源推送的数据超出了强制限制，则会出现限制和延迟的情况。

下图显示了一个 SKU 为 S1 且容量为 3 的时序见解环境。 它每天可以引入 300 万个事件。

![环境 SKU 当前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

假设此环境从一个事件中心引入消息，此事件中心的入口速率如下图所示：

![环境 SKU 当前容量](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

如图中所示，每日的入口速率约为 67,000 条消息。 此速率可大致转换为每分钟 46 条消息。 如果将每条事件中心消息平展为单个时序见解事件，此环境则不存在限制。 如果将每条事件中心消息平展为 100 个时序见解事件，则每分钟应引入 4,600 个事件。 一个容量为 3 的 S1 SKU 环境每分钟只能引入 2,100 个事件。 （每天 100 万个事件 = > 每分钟 700 个事件，3 个单位 => 每分钟 2100 个事件）。 因此会因为限制而导致出现延迟情况。 有关平展逻辑工作的深入介绍，请在[此处](time-series-insights-send-events.md#supported-json-shapes)参阅“支持的 JSON 形状”一节。

#### <a name="recommended-steps"></a>建议的步骤
若要解决延迟问题，请增加环境的 SKU 容量。 [如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)

### <a name="you-might-be-pushing-historical-data-and-hence-the-slow-ingress"></a>可能因为正在推送历史数据而导致引入缓慢
如果正在连接现有事件源，则此事件源中很可能已经包含事件中心 / IoT 中心的数据。 因此，环境将开始从事件源消息保持期最开始的时间点拉取数据。 此行为是默认行为，且无法被替代。 因此，可能会触发限制，并且需要一段时间才能捕获引入的历史数据。

#### <a name="recommended-steps"></a>建议的步骤
若要解决延迟问题，请执行以下步骤：
1. 将 SKU 容量增加到所允许的最大值（本例中为 10）。 容量增加后，引入进程的捕获速度将大大增加。 可以通过[时序见解门户](https://insights.timeseries.azure.com)中的可用性图表对我们的捕获速度进行直观地观察。 增加容量需要付费。
2. 延迟消除后，请将 SKU 容量降回到正常的入口速率。

## <a name="my-event-source-timestamp-property-name-setting-does-not-work"></a>我的事件源“时间戳属性名称”设置不起作用
请确保名称和值符合以下规则：
1. 时间戳属性名称区分大小写。
2. 来自事件源的时间戳属性值（作为 JSON 字符串）的格式应为 yyyy-MM-ddTHH:mm:ss.FFFFFFFK。 “2008-04-12T12:53Z”即是此类字符串的一个示例。
