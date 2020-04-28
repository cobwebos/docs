---
title: 诊断、排查和解决问题-Azure 时序见解
description: 本文介绍如何诊断、排查和解决 Azure 时序见解环境中的常见问题。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 4d9efa1ebf1a3e3b146c4f45b0e84047562141cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192708"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>诊断和解决时序见解环境中的问题

本文介绍你可能在 Azure 时序见解环境中遇到的问题。 本文将提供问题的潜在原因和解决方法。

## <a name="video"></a>视频

### <a name="learn-about-common-time-series-insights-challenges-and-mitigationsbr"></a>了解常见时序见解挑战和缓解措施</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>问题：未显示数据

如果[Azure 时序见解资源管理器](https://insights.timeseries.azure.com)中未显示数据，请考虑以下常见原因。

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因 A：事件源数据不是 JSON 格式

Azure 时序见解仅支持 JSON 数据。 有关 JSON 示例，请参阅[支持的 JSON 形状](./how-to-shape-query-json.md)。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B：事件源密钥缺少所需的权限

* 对于 Azure IoT 中心内的 IoT 中心，需要提供具有服务连接权限的密钥。 选择 " **iothubowner** " 或 "**服务**策略"。 两者都具有服务连接权限。

   [![IoT 中心“服务连接”权限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* 对于 Azure 事件中心内的事件中心，需要提供具有 "侦听" 权限的密钥。 "**读取**" 和 "**管理**" 策略都将起作用，因为它们都具有侦听权限。

   [![事件中心“侦听”权限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-time-series-insights"></a>原因 C：提供的使用者组不是时序见解独有的

注册 IoT 中心或事件中心时，必须设置用于读取数据的使用者组。 不能共享此使用者组。** 如果共享此使用者组，底层 IoT 中心或事件中心会随机自动断开某个读取者的连接。 请提供唯一的使用者组，供时序见解从中读取。

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>原因 D：环境刚刚预配

在环境及其数据首次创建之后的几分钟内，数据显示在时间序列见解资源管理器中。

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>问题：显示了一些数据，但缺少数据

如果数据仅部分显示并且数据似乎滞后，请考虑以下可能的问题。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因 A：你的环境正在受到限制

如果在创建包含数据的事件源后设置环境，则[限制](time-series-insights-environment-mitigate-latency.md)是一个常见问题。 Azure IoT 中心和 Azure 事件中心将数据存储最长 7 天。 时序见解总是以事件源（先进先出或*FIFO*）中的最早事件开始。

例如，如果某个事件源中有 500 万个事件，当你连接到 S1（单一单位时序见解环境）时，时序见解每天会读取大约 100 万个事件。 时序见解看上去遇到了 5 天的延迟。 但发生的情况是，环境受到限制。

如果事件源中有旧事件，可通过以下两种方式之一来解决限制：

- 更改事件源的保留限制，以帮助删除不想要显示在时序见解中的旧事件。
- 预配一个更大的环境大小（以单位数计），以提高旧事件的吞吐量。 在前面的示例中，如果一天将同一个 S1 环境增加到五个单位，环境应在一整天内及时完成。 如果稳定状态事件的生产每日为1000000或更少事件，可以在时序见解捕获后，将事件容量减少到一个单元。

强制限制基于环境的 SKU 类型和容量。 环境中的所有事件源都共享此容量。 如果 IoT 中心或事件中心的事件源推送的数据超出了强制的限制，则会遇到限制和延迟。

下图显示了一个 SKU 为 S1 且容量为 3 的时序见解环境。 它每天可以引入 300 万个事件。

[![环境容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

假设某个环境从事件中心引入消息。 它有大约67000消息的每日入口速率。 此速率相当于每分钟大约引入 46 条消息。

* 如果将每条事件中心消息平展为单个时序见解事件，则不会发生限制。
* 如果将每条事件中心消息平展为 100 个时序见解事件，则每分钟应引入 4,600 个事件。

容量为 3 的 S1 SKU 环境每分钟只能流入 2,100 个事件（每天 100 万个事件 = 每分钟 700 个事件，3 个单位 = 每分钟 2,100 个事件）。

若要获得对平展逻辑工作方式的深入了解，请参阅[支持的 JSON 形状](./how-to-shape-query-json.md)。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>针对过度限制的建议解决方法

若要解决延迟问题，请增加环境的 SKU 容量。 有关详细信息，请参阅[缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B：初始引入历史数据减慢进入速度

如果连接现有事件源，则 IoT 中心或事件中心可能已包含数据。 环境将开始从事件源消息保留期的起始时间点拉取数据。 这是默认的处理方式，不能将其覆盖。 可以触发限制。 限制可能需要一段时间才能赶上进度，因为要引入历史数据。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>针对大型初始引入的建议解决方法

解决滞后问题：

1. 将 SKU 容量增大到允许的最大值（本例中为 10）。 增大容量后，流入进程很快就能开始赶上进度。 系统会为增加容量收费。 若要观察赶上进度的速度，可以查看[时序见解资源管理器](https://insights.timeseries.azure.com)中的可用性图表。

2. 消除滞后问题之后，将 SKU 容量降低至正常流入速率。

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>问题：以前显示的数据，但不再显示

如果时序见解不再引入数据，但事件仍流式传输到 Iot 中心或事件中心，则请考虑此可能的原因。

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>原因 A：已重新生成中心访问密钥，环境需要更新

如果创建事件源时提供的密钥不再有效，则会出现此问题。 你会在中心看到遥测数据，但不会在时序见解中收到任何入口接收的消息。 如果不确定是否已重新生成密钥，可以在事件中心的活动日志中搜索 "创建或更新命名空间授权规则"。 对于 IoT 中心，搜索 "创建或更新 IotHub 资源"。

若要用新密钥更新时序见解环境，请在 Azure 门户中打开中心资源，然后复制新密钥。 请参阅时序见解资源，并选择 "**事件源**"：

   [![选择事件源](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

选择已停止引入的事件源，粘贴新密钥，然后选择 "**保存**"：

   [![粘贴新密钥](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>问题：事件源的时间戳属性名称设置不起作用

确保来自事件源的 timestamp 属性值为 JSON 字符串，格式为_yyyy-mm-dd-yyyy-mm-ddthh： MM： ss。SS'.'FFFFFFFK_。 下面是一个示例： **2008-04-12T12： 53Z**。

请记住，时间戳属性名称区分大小写。

使用时序见解资源管理器是确保捕获时间戳属性名称并让其正常运行的最简单方法。 在时序见解资源管理器中使用图表，并在输入时间戳属性名称之后选择一个时间段。 右键单击所选内容，然后选择 "**浏览事件**"。

第一个列标头应是时间戳属性名称。 在单词 **Timestamp** 旁边，将显示 **($ts)**。

不会显示以下值：

- *（abc）*：指示时序见解正在将数据值作为字符串读取。
- *日历图标*：指示时序见解正在将数据值读取为日期时间值。
- *#*：指示时序见解正在将数据值作为整数读取。

## <a name="next-steps"></a>后续步骤

- 了解[如何减少 Azure 时序见解中的延迟](time-series-insights-environment-mitigate-latency.md)。

- 了解[如何缩放时序见解环境](time-series-insights-how-to-scale-your-environment.md)。
