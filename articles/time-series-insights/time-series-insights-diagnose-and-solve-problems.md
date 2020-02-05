---
title: 诊断、排查和解决问题-Azure 时序见解 |Microsoft Docs
description: 本文介绍如何诊断、排查和解决 Azure 时序见解环境中的常见问题。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 35b330f27ba87aa18ce2c2f275a7b19fdae3cb65
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024409"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>诊断和解决时序见解环境中的问题

本文描述你可能会在 Azure 时序见解环境中遇到的一些问题。 本文将提供问题的潜在原因和解决方法。

## <a name="video"></a>视频

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>了解常见时序见解客户难题和缓解措施。</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>问题：未显示数据

[Azure 时序见解资源管理器](https://insights.timeseries.azure.com)中可能会出于多种常见原因而不显示数据：

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因 A：事件源数据不是 JSON 格式

Azure 时序见解仅支持 JSON 数据。 有关 JSON 示例，请参阅[支持的 json 形状](./how-to-shape-query-json.md)。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B：事件源密钥缺少所需的权限

* 对于 Azure IoT 中心内的某个 IoT 中心，必须提供具有“服务连接”权限的密钥。 选择**iothubowner**或**服务**策略，因为两者都具有**服务连接**权限。

   [![IoT 中心服务连接权限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* 对于 Azure 事件中心内的某个事件中心，必须提供具有“侦听”权限的密钥。 **read** 或 **manage** 策略可正常运行，因为两者都具有“侦听”权限。

   [![事件中心侦听权限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>原因 C：提供的使用者组不适用于时序见解

注册 IoT 中心或事件中心时，必须设置用于读取数据的使用者组。 不能共享此使用者组。 如果共享此使用者组，底层 IoT 中心或事件中心会随机自动断开某个读取者的连接。 请提供唯一的使用者组，供时序见解从中读取。

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>原因 D：环境刚刚预配

数据将在环境中的几分钟内显示，并在第一次创建数据后出现在时序见解资源管理器中。

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>问题：显示了一些数据，但缺少数据

如果仅显示了一部分数据，并且数据显示滞后，则应该考虑到几种可能性。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因 A：你的环境正在受到限制

如果在创建包含数据的事件源后设置环境，则[限制](time-series-insights-environment-mitigate-latency.md)是一个常见问题。 Azure IoT 中心和 Azure 事件中心将数据存储最长 7 天。 时序见解始终最先处理事件源中的最早事件（先进先出 (*FIFO*)）。

例如，如果某个事件源中有 500 万个事件，当你连接到 S1（单一单位时序见解环境）时，时序见解每天会读取大约 100 万个事件。 时序见解看上去遇到了 5 天的延迟。 但是，实际原因是环境受到了限制。

如果事件源中有旧事件，可通过以下两种方式之一来解决限制：

- 更改事件源的保留限制，以帮助删除不想要显示在时序见解中的旧事件。
- 预配一个更大的环境大小（以单位数计），以提高旧事件的吞吐量。 沿用上面的示例，如果在某一天将同一 S1 环境增大到 5 个单位，则环境在一天内就能赶上进度。 如果每天以稳定状态生成 100 万或更少的事件，则赶上进度后，可将事件容量减少为 1 个单位。

将基于环境 SKU 类型和容量对环境进行强制限制。 环境中的所有事件源都共享此容量。 如果 IoT 中心或事件中心的事件源推送的数据超出了强制的限制，则会遇到限制和延迟。

下图显示了一个 SKU 为 S1 且容量为 3 的时序见解环境。 它每天可以引入 300 万个事件。

[![环境 SKU 当前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

例如，假设环境从事件中心引入消息。 每日流入速率大约为 67,000 条消息。 此速率相当于每分钟大约引入 46 条消息。 

* 如果将每条事件中心消息平展为单个时序见解事件，则不会发生限制。 
* 如果将每条事件中心消息平展为 100 个时序见解事件，则每分钟应引入 4,600 个事件。 

容量为 3 的 S1 SKU 环境每分钟只能流入 2,100 个事件（每天 100 万个事件 = 每分钟 700 个事件，3 个单位 = 每分钟 2,100 个事件）。 

若要深入了解平展逻辑的工作原理，请阅读[支持的 JSON 形状](./how-to-shape-query-json.md)。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>针对过度限制的建议解决方法

若要解决延迟问题，请增加环境的 SKU 容量。 有关详细信息，请参阅[缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B：初始引入历史数据减慢进入速度

如果连接现有事件源，则 IoT 中心或事件中心可能已包含数据。 环境将开始从事件源消息保留期的起始时间点拉取数据。 此默认处理不能被重写。 可以触发限制。 限制可能需要一段时间才能赶上进度，因为要引入历史数据。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>针对大型初始引入的建议解决方法

解决滞后问题：

1. 将 SKU 容量增大到允许的最大值（本例中为 10）。 增大容量后，流入进程很快就能开始赶上进度。 增加容量需要付费。 若要观察赶上进度的速度，可以查看[时序见解资源管理器](https://insights.timeseries.azure.com)中的可用性图表。

2. 消除滞后问题之后，将 SKU 容量降低至正常流入速率。

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>问题：我的事件源的时间戳属性名称设置不起作用

请确保时间戳属性名称和值符合以下规则：

* 时间戳属性名称区分大小写。
* 来自事件源的时间戳属性值（JSON 字符串）的格式应为 _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_。 例如 **2008-04-12T12:53Z**。

使用时序见解资源管理器是确保捕获时间戳属性名称并让其正常运行的最简单方法。 在时序见解资源管理器中使用图表，并在输入时间戳属性名称之后选择一个时间段。 右键单击所做的选择，然后选择“浏览事件”选项。

第一个列标头应是时间戳属性名称。 将显示 "**时间戳**" 旁边的 **（$ts）** 。

将不显示以下值：

- *（abc）* ：指示时序见解正在将数据值作为字符串读取。
- *日历图标*：指示时序见解正在将数据值作为*datetime*读取。
- *#* ：指示时序见解正在将数据值作为整数读取。

## <a name="next-steps"></a>后续步骤

- 了解[如何减少 Azure 时序见解中的延迟](time-series-insights-environment-mitigate-latency.md)。

- 了解[如何扩展时序见解环境](time-series-insights-how-to-scale-your-environment.md)。