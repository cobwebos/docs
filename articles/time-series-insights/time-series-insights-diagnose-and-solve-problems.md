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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4b8d5fdab1744b2db658917f91d6dac05db30d2f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/12/2017

---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>在时序见解环境中诊断并解决问题

## <a name="i-dont-see-my-data"></a>看不到我的数据
在 [Azure 时序见解门户](https://insights.timeseries.azure.com)的环境中看不到数据可能是因以下原因。

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>事件源没有 JSON 格式的数据
Azure 时序见解目前仅支持 JSON 数据。 有关 JSON 示例，请参阅[支持的 JSON 形状](time-series-insights-send-events.md#supported-json-shapes)。

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>注册事件源时，未提供具有所需权限的密钥
* 对于 IoT 中心，需提供具有“服务连接”权限的密钥。

   ![IoT 中心服务连接权限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   如上图所示，“iothubowner”和“服务”策略均可使用，因为两者都具有“服务连接”权限。
* 对于事件中心，需提供具有“侦听”权限的密钥。

   ![事件中心侦听权限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   如上图所示，“读取”和“管理”策略均可使用，因为两者都具有“侦听”权限。

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>提供的使用者组并非时序见解所独有
对于 IoT 中心或事件中心，我们要求在注册期间指定应用于读取数据的使用者组。 绝对不能共享此使用者组。 如果共享了此组，则基础事件中心会随机自动断开一个读取器的连接。

## <a name="i-see-my-data-but-theres-a-lag"></a>我能看到数据，但存在延迟
在[时序见解门户](https://insights.timeseries.azure.com)的环境中只看到部分数据可能是由以下原因所致。

### <a name="your-environment-is-getting-throttled"></a>环境受到了限制
将基于环境 SKU 类型和容量对环境进行强制限制。 环境中的所有事件源都共享此容量。 如果 IoT 中心或事件中心的事件源推送的数据超出了强制的限制，则会出现限制和延迟的情况。

下图显示了一个 SKU 为 S1 且容量为 3 的时序见解环境。 它每天可以引入 300 万个事件。

![环境 SKU 当前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

假设此环境从一个事件中心引入消息，下图显示了入口速率：

![事件中心入口速率示例](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

如图中所示，每日的入口速率约为 67,000 条消息。 此速率可大致转换为每分钟 46 条消息。 如果将每条事件中心消息平展为单个时序见解事件，此环境则不存在限制。 如果将每条事件中心消息平展为 100 个时序见解事件，则每分钟应引入 4,600 个事件。 容量为 3 的 S1 SKU 环境每分钟只能引入 2,100 个事件（每天 100 万个 = 每分钟 700 个，3 个单位 = 每分钟 2,100 个）。 因此会因为限制而导致出现延迟情况。 

有关平展逻辑工作原理的深入介绍，请参阅[支持的 JSON 形状](time-series-insights-send-events.md#supported-json-shapes)。

#### <a name="recommended-steps"></a>建议的步骤
若要解决延迟问题，请增加环境的 SKU 容量。 有关详细信息，请参阅[如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)。

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>你正在推送历史数据，导致引入速度缓慢
如果正在连接现有事件源，则 IoT 中心或事件中心内可能已存在数据。 因此，环境将开始从事件源消息保留期的起始时间点拉取数据。 

此行为是默认行为，且无法被替代。 可能会触发限制，且需要一段时间才能捕获引入的历史数据。

#### <a name="recommended-steps"></a>建议的步骤
若要解决延迟问题，请按以下步骤操作：
1. 将 SKU 容量增加到所允许的最大值（本例中为 10）。 容量增加后，引入进程的捕获速度将大大增加。 可通过[时序见解门户](https://insights.timeseries.azure.com)中的可用性图表直接观察捕获速度。 增加容量需要付费。
2. 延迟消除后，请将 SKU 容量降回到正常的入口速率。

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>我的事件源“时间戳属性名称”设置不起作用
请确保名称和值符合以下规则：
* 时间戳属性名称区分大小写。
* 来自事件源的时间戳属性值（作为 JSON 字符串）的格式应为 yyyy-MM-ddTHH:mm:ss.FFFFFFFK。 “2008-04-12T12:53Z”即是此类字符串的一个示例。

