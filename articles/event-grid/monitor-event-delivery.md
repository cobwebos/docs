---
title: 查看 Azure 事件网格指标并设置警报
description: 本文介绍如何使用 Azure 门户查看 Azure 事件网格主题和订阅的指标，并对其创建警报。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: 75311675ae24f4836ed8f1adb8a7d9802b4d7f0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390595"
---
# <a name="monitor-event-grid-message-delivery"></a>监视事件网格消息传送 
本文介绍如何使用门户查看事件网格主题和订阅的指标，并为其创建警报。 

## <a name="metrics"></a>指标

门户将显示用于表示事件消息传送状态的指标。

对于主题，以下是一些指标：

* **发布成功**：事件已成功发送到主题，并且以 2xx 响应进行处理。
* **发布失败**：事件已发送到主题，但被拒绝并返回错误代码。
* **不匹配**：事件已成功发布到主题，但与事件订阅不匹配。 已删除该事件。

对于订阅，以下是一些指标：

* **传送成功**：事件已成功传送到订阅的终结点，但收到 2xx 响应。
* **传送失败**：服务每次尝试传送并且事件处理程序未返回成功的 2xx 代码时，“传送失败”**** 计数器就会相应递增。 如果我们尝试多次传送同一事件并失败，则每次失败都会使“传送失败”**** 计数器相应递增。
* **事件过期**：未传送事件，并且已发送所有重试尝试。 已删除该事件。
* **已匹配事件**：事件订阅已匹配主题中的事件。

    > [!NOTE]
    > 有关指标的完整列表，请参阅 [Azure 事件网格支持的指标](metrics.md)。

## <a name="view-custom-topic-metrics"></a>查看自定义主题指标

如果已发布自定义主题，则可以查看其指标。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在主题的搜索栏中，键入 "**事件网格主题**"，然后从下拉列表中选择 "**事件网格主题**"。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="搜索并选择“事件网格主题”":::
3. 从主题列表中选择自定义主题。 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="选择自定义主题":::
4. 在**事件网格主题**页上查看自定义事件主题的指标。 下图中的 "**概要**" 部分显示了资源组、订阅等。 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="查看事件指标":::

您可以通过使用**事件网格主题**页的 "**指标**" 选项卡，创建具有支持的度量值的图表。

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="主题-"指标" 页":::

若要了解有关指标的详细信息，请参阅[中的指标 Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)

有关示例，请参阅**已发布事件**指标的度量值图表。

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="已发布事件的指标":::


## <a name="view-subscription-metrics"></a>查看订阅指标
1. 按照上一部分中的步骤，导航到**事件网格主题**页。 
2. 从底部窗格中选择订阅，如以下示例中所示。 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="选择事件订阅":::    

    你还可以在 Azure 门户的搜索栏中搜索**事件网格订阅**，选择 "**主题类型**"、"**订阅**" 和 "**位置**" 以查看事件订阅。 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="从 "事件网格订阅" 页选择事件订阅":::        

    对于自定义主题，请选择**事件网格主题**作为**主题类型**。 对于 "系统主题"，请选择 Azure 资源的类型，例如**存储帐户（Blob、GPv2）**。 
3. 请在 "主页" 页上查看订阅的度量值。 可以查看过去1小时、6小时、12小时、1天、7天或30天的**常规**、**错误**、**延迟**和**死信**指标。 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="订阅主页上的指标":::    

## <a name="view-system-topic-metrics"></a>查看系统主题指标

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在主题的搜索栏中，键入 "**事件网格系统主题**"，然后从下拉列表中选择 "**事件网格系统主题**"。 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="搜索和选择事件网格系统主题":::
3. 从主题列表中选择你的系统主题。 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="选择系统主题":::
4. 在**事件网格系统主题**页上查看系统主题的指标。 下图中的 "**概要**" 部分显示了资源组、订阅等。 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="在 "概述" 页上查看系统主题指标":::

您可以通过使用**事件网格主题**页的 "**指标**" 选项卡，创建具有支持的度量值的图表。

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="系统主题-"指标" 页":::

若要了解有关指标的详细信息，请参阅[中的指标 Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 若要了解如何创建有关指标和活动日志操作的警报，请参阅[设置警报](set-alerts.md)。
- 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
