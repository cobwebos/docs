---
title: 对预览版环境进行诊断和故障排除 - Azure 时序见解 | Microsoft Docs
description: 了解如何对 Azure 时序见解预览版环境进行诊断和故障排除。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80152634"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>对预览版环境进行诊断和故障排除

本文汇总了在使用 Azure 时序见解预览版环境时可能会遇到的多个常见问题。 本文还介绍了每个问题的可能原因和解决方案。

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>问题：在预览版资源管理器中找不到环境

如果无权访问时序见解环境，则可能会发生此问题。 用户需要读者级别访问角色才能查看其时序见解环境。 若要验证当前访问级别并授予其他访问权限，请在 [Azure 门户](https://portal.azure.com/)中转到时序见解资源上的“数据访问策略”部分。 

  [![验证数据访问策略。](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>问题：预览版资源管理器中未显示数据

有几种常见原因导致数据可能不会出现在 [Azure 时序见解预览版资源管理器](https://insights.timeseries.azure.com/preview)中。

- 事件源可能未接收数据。

    验证事件源（即事件中心或 IoT 中心）是否从标记或实例接收数据。 若要进行验证，请转到 Azure 门户中资源的概览页。

    [![查看仪表板指标概述。](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- 事件源数据不是 JSON 格式。

    时序见解仅支持 JSON 数据。 有关 JSON 示例，请阅读[支持的 JSON 形状](./how-to-shape-query-json.md)。

- 事件源密钥缺少所需权限。

  * 对于 IoT 中心，需提供具有“服务连接”  权限的密钥。

    [![验证 IoT 中心权限。](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * “iothubowner”和“服务”策略均可使用，因为两者都具有“服务连接”权限    。

  * 对于事件中心，需提供具有“侦听”  权限的密钥。
  
    [![查看事件中心权限。](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * “读取”和“管理”策略均可使用，因为两者都具有“侦听”权限    。

- 提供的使用者组并非时序见解所独有。

    IoT 中心或事件中心注册期间，请指定用于读取数据的使用者组。 此使用者组必须在环境中独一无二。 如果共享了此使用者组，则基础事件中心会随机自动断开一个读取器的连接。 请提供唯一的使用者组，供时序见解从中读取。

- 在预配时指定的时序 ID 属性不正确、缺失或为 null。

    如果在预配环境时时序 ID 属性配置不正确，则可能会发生此问题。 有关详细信息，请阅读[选择时序 ID 的最佳做法](./time-series-insights-update-how-to-id.md)。 目前无法更新现有时序见解环境来使用其他时序 ID。

## <a name="problem-some-data-shows-but-some-is-missing"></a>问题：显示了一些数据，但是还有一些数据缺失

可能在发送数据时没有提供时序 ID。

- 如果在发送事件时有效负载中没有时序 ID 字段，则可能会发生此问题。 有关详细信息，请阅读[支持的 JSON 形状](./how-to-shape-query-json.md)。
- 可能因环境受限而发生此问题。

    > [!NOTE]
    > 目前，时序见解支持的最大引入速率为 6 Mbps。

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>问题：以前可以显示数据，但引入现已停止

- 可能已重新生成事件源密钥，但预览版环境需要新的事件源密钥。

如果创建事件源时提供的密钥不再有效，则会出现此问题。 你会在中心看到遥测数据，但不会在时序见解中收到入口接收的消息。 如果不确定是否重新生成了密钥，可以在事件中心的活动日志中搜索“创建或更新命名空间授权规则”或“为 IoT 中心创建或更新 IotHub 资源”。 

若要使用新密钥更新时序见解预览版环境，请在 Azure 门户中打开中心资源并复制新密钥。 导航到 TSI 资源，单击“事件源”。 

   [![更新密钥。](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

选择已停止从其引入的事件源，粘贴新密钥，然后单击“保存”。

   [![更新密钥。](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>问题：事件源的时间戳属性名称不起作用

请确保名称和值符合以下规则：

* Timestamp 属性名称区分大小写。
* 来自事件源的 Timestamp 属性值（采用 JSON 字符串形式）的格式为 `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`。 `“2008-04-12T12:53Z”` 是此类字符串的一个示例。

若要确保捕获 Timestamp 属性名称并让其正常运行，最简单的方法是使用时序见解预览版资源管理器。 在时序见解预览版资源管理器中使用此图表，在提供 Timestamp 属性名称以后选择一个时间段。 右键单击所做的选择，然后选择“浏览事件”选项。  第一个列标头为 Timestamp 属性名称。 它应该有 `($ts)` 位于 `Timestamp` 一词的旁边，而不是：

* `(abc)`，指示时序见解将数据值作为字符串来读取。
* **日历**图标，指示时序见解将数据值作为日期/时间来读取。
* `#`，指示时序见解将数据值作为整数来读取。

如果 Timestamp 属性未显式指定，则会将事件的 IoT 中心或事件中心的“排队时间”用作默认的时间戳。

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>问题：我无法在资源管理器中查看我的暖存储中的数据

- 你可能最近预配了你的暖存储，数据仍在流入。
- 你可能已删除了暖存储，这种情况下，你已经丢失了数据。

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>问题：无法查看或编辑时序模型

- 你可能在访问时序见解 S1 或 S2 环境。

   时序模型仅在即用即付环境中受支持。 有关如何从时序见解预览资源管理器访问 S1 或 S2 环境的详细信息，请阅读[资源管理器中的可视化数据](./time-series-insights-update-explorer.md)。

   [![环境中没有事件。](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- 你可能无权查看和编辑此模型。

   用户需要有参与者级别访问权限才能编辑和查看其时序模型。 若要验证当前访问级别并授予其他访问权限，请在 Azure 门户中转到时序见解资源上的“数据访问策略”部分。****

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>问题：预览资源管理器中的所有实例都缺少父项

如果环境未定义时序模型层次结构，则可能会发生此问题。 有关详细信息，请阅读[使用时序模型](./time-series-insights-update-how-to-tsm.md)。

  [![Unparented 实例将显示警告。](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 阅读[使用时序模型](./time-series-insights-update-how-to-tsm.md)。

- 了解[支持的 JSON 形状](./how-to-shape-query-json.md)。

- 查看 Azure 时序见解预览版中的[规划和限制](./time-series-insights-update-plan.md)。
