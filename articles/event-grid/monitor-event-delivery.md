---
title: 监视 Azure 事件网格消息传送
description: 本文介绍如何使用 Azure 门户查看 Azure 事件网格消息的传送状态。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 7a01ab91fe84aaa1fe55018754eddbf8b8f89643
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890854"
---
# <a name="monitor-event-grid-message-delivery"></a>监视事件网格消息传送 

本文介绍如何使用门户查看事件传送的状态。

事件网格提供持久传送。 它会将每个订阅的每条消息至少发送一次。 事件会立即发送到每个订阅的已注册 webhook。 如果 webhook 在首次发送后 60 秒内未确认已接收事件，则事件网格会重试事件传送。

有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。

## <a name="delivery-metrics"></a>传送指标

门户将显示用于表示事件消息传送状态的指标。

对于主题，以下是一些指标：

* **发布成功**：事件已成功发送到主题，并且以 2xx 响应进行处理。
* **发布失败**：事件已发送到主题，但被拒绝并返回错误代码。
* **不匹配**：事件已成功发布到主题，但与事件订阅不匹配。 已删除该事件。

对于订阅，以下是一些指标：

* **传送成功**：事件已成功传送到订阅的终结点，但收到 2xx 响应。
* **传递失败**：每次服务尝试传递并且事件处理程序未返回成功的2xx 代码时，**传递失败**计数器就会增加。 如果尝试多次传递同一事件并失败，则每次出现故障时，**传递失败**计数器就会增加。
* **事件过期**：未传送事件，并且已发送所有重试尝试。 已删除该事件。
* **已匹配事件**：事件订阅已匹配主题中的事件。

    > [!NOTE]
    > 有关指标的完整列表，请参阅[Azure 事件网格支持的指标](metrics.md)。

## <a name="event-subscription-status"></a>事件订阅状态

若要查看事件订阅的指标，可以按订阅类型搜索或按特定资源的订阅搜索。

若要按事件订阅类型搜索，请选择“所有服务”****。

![选择所有服务](./media/monitor-event-delivery/all-services.png)

搜索事件网格****，并从可用选项中选择“事件网格订阅”****。

![搜索事件订阅](./media/monitor-event-delivery/search-and-select.png)

按事件类型、订阅和位置进行筛选。 针对要查看的订阅选择“指标”****。

![筛选事件订阅](./media/monitor-event-delivery/filter-events.png)

查看事件主题和订阅的指标。

![查看事件指标](./media/monitor-event-delivery/subscription-metrics.png)

若要查找特定资源的指标，请选择该资源。 然后，选择“事件”****。

![选择资源的事件](./media/monitor-event-delivery/select-events.png)

你会看到该资源的订阅指标。

## <a name="custom-event-status"></a>自定义事件状态

如果已发布自定义主题，则可以查看其指标。 选择该主题的资源组，然后选择主题。

![选择自定义主题](./media/monitor-event-delivery/select-custom-topic.png)

查看自定义事件主题的指标。

![查看事件指标](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>设置警报

可以为自定义主题和事件域设置针对主题和域级别指标的警报。 在“概览”边栏选项卡中，从左侧的资源菜单选择“警报”****，以便查看、管理和创建警报规则。 [了解有关 Azure Monitor 警报的详细信息](../azure-monitor/platform/alerts-overview.md)

![查看事件指标](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>后续步骤

* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
