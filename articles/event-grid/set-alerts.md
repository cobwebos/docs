---
title: 为 Azure 事件网格指标和活动日志操作设置警报
description: 本文介绍如何创建有关 Azure 事件网格指标和活动日志操作的警报。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: spelluru
ms.openlocfilehash: 1a2eacb5fa03ea2a5a8ba2d38d9b3e7dea315890
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85412832"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>针对 Azure 事件网格指标和活动日志设置警报
本文介绍如何创建有关 Azure 事件网格指标和活动日志操作的警报。 可以针对 Azure 事件网格资源（主题和域）的发布和传递指标创建警报。 对于系统主题，[使用 "**指标**" 页创建警报](#create-alerts-using-the-metrics-page)。

## <a name="create-alerts-on-dead-lettered-events"></a>针对死信事件创建警报
下面的过程演示如何针对自定义主题以**死信事件**度量值创建警报。 在此示例中，如果主题的 "死信" 事件计数超过10个，则会向 Azure 资源组所有者发送一封电子邮件。 

1. 在主题的 "**事件网格主题**" 页上，选择左侧菜单中的 "**警报**"，然后选择 " **+ 新建警报规则**"。 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="“警报”页 -“新建警报规则”按钮":::
2. 在 "**创建警报规则**" 页上，验证是否已为资源选择了主题。 然后单击 "**选择条件**"。 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text=""警报" 页-选择条件":::    
3. 在 "**配置信号逻辑**" 页上，执行以下步骤：
    1. 选择指标或活动日志条目。 在此示例中，选择了 "**死信事件**"。 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="选择死信事件":::        
    2. 选择维度（可选）。 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="配置信号逻辑":::        

        > [!NOTE]
        > 你可以选择 " **+** **EventSubscriptionName** " 按钮，以指定用于筛选事件的事件订阅名称。 
    3. 向下滚动。 在 "**警报逻辑**" 部分中，选择一个**运算符**、**聚合类型**并输入**阈值**，然后选择 "**完成**"。 在此示例中，当 "死信总数" 事件计数大于10时，会触发警报。 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="警报逻辑":::                
4. 返回 "**创建警报规则**" 页，单击 "**选择操作组**"。

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="选择操作组按钮":::
5. 选择工具栏上的 "**创建操作组**" 以创建新的操作组。 你还可以选择现有的操作组。        
6. 在 "**添加操作组**" 页上，执行以下步骤：
    1. 输入**操作组的名称**。
    1. 为操作组输入“短名称”。
    1. 选择要在其中创建操作组的**Azure 订阅**。
    1. 选择要在其中创建操作组的**Azure 资源组**。
    1. 输入**操作的名称**。 
    1. 选择 "**操作类型**"。 在此示例中，选择了 "**电子邮件 Azure 资源管理器角色**"，特别是 "**所有者**" 角色。 
    1. 选择“确定”关闭页面。 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="“添加操作组”页":::                   
7. 返回 "**创建警报规则**" 页，输入警报规则的名称，然后选择 "**创建警报规则**"。

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="警报规则名称":::  
8. 现在，在主题的 "**警报**" 页上，可以看到一个用于管理警报规则的链接（如果尚无警报）。 如果有警报，请在工具栏上选择 "**管理器警报规则**"。  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="管理警报":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>创建有关其他指标或活动日志操作的警报
上一部分介绍了如何针对死信事件创建警报。 针对其他指标或活动日志操作创建警报的步骤类似。 

例如，若要针对传递失败事件创建警报，请在 "**配置信号逻辑**" 页上选择 "**传递失败的事件**"。 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="选择传递失败的事件":::


## <a name="create-alerts-using-the-metrics-page"></a>使用 "指标" 页创建警报
你还可以使用 "**指标**" 页创建警报。 步骤类似。 对于系统主题，只能使用 "**指标**" 页来创建警报，因为**警报**页面不可用。 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text=""指标" 页-"创建警报" 按钮":::   
    

> [!NOTE]
> 本文不介绍可用于创建警报的所有不同步骤和组合。 有关警报的概述，请参阅[警报概述](../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>后续步骤

* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
