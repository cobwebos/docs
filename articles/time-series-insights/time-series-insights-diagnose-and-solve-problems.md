---
title: 诊断并解决 Azure 时序见解中的问题 | Microsoft Docs
description: 本文介绍如何诊断、排查和解决可能在 Azure 时序见解环境中遇见的常见问题。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: venkatja
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: dbd32d57206b611a37b5349e5971d2efe272ee1a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292865"
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>在时序见解环境中诊断并解决问题

## <a name="problem-1-no-data-is-shown"></a>问题 1：未显示数据
在 [Azure 时序见解资源管理器](https://insights.timeseries.azure.com)中未看见数据有多个常见原因：

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>可能的原因 A：事件源数据不是 JSON 格式
Azure 时序见解仅支持 JSON 数据。 有关 JSON 示例，请参阅[支持的 JSON 形状](time-series-insights-send-events.md#supported-json-shapes)。

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>可能的原因 B：事件源密钥缺少所需权限
* 对于 IoT 中心，需提供具有“服务连接”权限的密钥。

   ![IoT 中心服务连接权限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   如上图所示，“iothubowner”和“服务”策略均可使用，因为两者都具有“服务连接”权限。
   
* 对于事件中心，需提供具有“侦听”权限的密钥。

   ![事件中心侦听权限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   如上图所示，“读取”和“管理”策略均可使用，因为两者都具有“侦听”权限。

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>可能的原因 C：提供的使用者组不是时序见解独有的
IoT 中心或事件中心注册期间，指定应用于读取数据的使用者组。 绝对不能共享此使用者组。 如果共享了此使用者组，则基础事件中心会随机自动断开一个读取器的连接。 请提供唯一的使用者组，供时序见解从中读取。

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>问题 2：显示了一些数据，但是还有一些数据缺失
当可以看见部分数据但数据滞后时，需要考虑几种可能性：

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>可能的原因 A：环境受到了限制
在使用数据创建某个事件源以后，如果预配了环境，则常常出现此问题。  Azure IoT 中心和事件中心存储数据的时间最多为七天。  在事件源中，TSI 将始终从最早的事件开始（先进先出）。  因此，如果你在某个事件源中有五百万个事件，则当你连接到 S1（单个单位 TSI 环境）时，TSI 每天会读取大约一百万个事件。  初看起来，似乎 TSI 会经历五天的延迟。  实际发生的是，环境会受到限制。  如果在事件源中有旧的事件，则可通过以下两种方式之一来访问：

- 更改事件源的保留限制，以便去除不希望其显示在 TSI 中的旧事件
- 预配一个更大的环境大小（就单元数目而言），以便增加旧事件的吞吐量。  以上面的示例为例，如果将该 S1 环境增加到一天五个单元，则环境现在应该在当天就能够赶上来。  如果事件生成稳定在每天 1 百万（或更少）个事件，则可在赶上来以后将事件容量减少到一个单元。  

将基于环境 SKU 类型和容量对环境进行强制限制。 环境中的所有事件源都共享此容量。 如果 IoT 中心或事件中心的事件源推送的数据超出了强制的限制，则会出现限制和延迟的情况。

下图显示了一个 SKU 为 S1 且容量为 3 的时序见解环境。 它每天可以引入 300 万个事件。

![环境 SKU 当前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

例如，假设此环境从事件中心引入消息。 请观察下图中显示的入口速率：

![事件中心入口速率示例](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

如图中所示，每日的入口速率约为 67,000 条消息。 此速率可大致转换为每分钟 46 条消息。 如果将每条事件中心消息平展为单个时序见解事件，此环境则不存在限制。 如果将每条事件中心消息平展为 100 个时序见解事件，则每分钟应引入 4,600 个事件。 容量为 3 的 S1 SKU 环境每分钟只能引入 2,100 个事件（每天 100 万个 = 每分钟 700 个，3 个单位 = 每分钟 2,100 个）。 因此会因为限制而导致出现延迟情况。 

有关平展逻辑工作原理的深入介绍，请参阅[支持的 JSON 形状](time-series-insights-send-events.md#supported-json-shapes)。

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>针对过多限制的建议解决方案步骤
若要解决延迟问题，请增加环境的 SKU 容量。 有关详细信息，请参阅[如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)。

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>可能的原因 B：初始引入历史数据导致引入速度缓慢
如果正在连接现有事件源，则 IoT 中心或事件中心内可能已存在数据。 环境将开始从事件源消息保留期的起始时间点拉取数据。

此行为是默认行为，且无法被替代。 可能会触发限制，且需要一段时间才能捕获引入的历史数据。

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>针对大型初始引入的建议解决方案步骤
若要解决延迟问题，请按以下步骤操作：
1. 将 SKU 容量增加到所允许的最大值（本例中为 10）。 容量增加后，引入进程的捕获速度将大大增加。 可通过[时序见解资源管理器](https://insights.timeseries.azure.com)中的可用性图表直接观察捕获速度。 增加容量需要付费。
2. 延迟消除后，请将 SKU 容量降回到正常的入口速率。

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>问题 3：我的事件源“时间戳属性名称”设置不起作用
请确保名称和值符合以下规则：
* 时间戳属性名称区分大小写。
* 来自事件源的时间戳属性值（作为 JSON 字符串）的格式应为 yyyy-MM-ddTHH:mm:ss.FFFFFFFK。 “2008-04-12T12:53Z”即是此类字符串的一个示例。

若要确保捕获时间戳属性名称并让其正常运行，最简单的方法是使用 TSI 资源管理器。  在 TSI 资源管理器中，使用此图表，在提供时间戳属性名称以后选择一个时间段。  右键单击所做的选择，然后选择“浏览事件”选项。  第一个列标题应该是时间戳属性名称，它应该在 *Timestamp* 单词旁边有 *($ts)*，而不是：
- *(abc)*，指示 TSI 正在将数据值作为字符串来读取
- 日历图标，指示 TSI 正在将数据值作为日期/时间来读取
- *#*，指示 TSI 正在将数据值作为整数来读取


## <a name="next-steps"></a>后续步骤
- 有关其他帮助，请在 [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights)或 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights) 中展开交流。 
- 也可以使用 [Azure 支持](https://azure.microsoft.com/support/options/)获取辅助支持选项。
