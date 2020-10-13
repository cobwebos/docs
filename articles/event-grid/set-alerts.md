---
title: 为 Azure 事件网格指标和活动日志操作设置警报
description: 本文介绍如何创建有关 Azure 事件网格指标和活动日志操作的警报。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 615ebef749be49822a09470eb0d47d8eb31b9a5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119134"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>设置有关 Azure 事件网格指标和活动日志的警报
本文介绍如何创建有关 Azure 事件网格指标和活动日志操作的警报。 可以针对 Azure 事件网格资源（主题和域）的发布和交付这两种指标创建警报。 对于系统主题，请[使用“指标”页创建警报](#create-alerts-using-the-metrics-page)。

## <a name="create-alerts-on-dead-lettered-events"></a>创建有关死信事件的警报
以下过程演示如何针对自定义主题的死信事件指标创建警报。 在此示例中，如果某个主题的死信事件计数超过 10，则会向 Azure 资源组所有者发送一封电子邮件。 

1. 在主题的“事件网格主题”页上，选择左侧菜单中的“警报”，然后选择“+ 新建警报规则”  。 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="“警报”页 -“新建警报规则”按钮":::
2. 在“创建警报规则”页上，验证是否已为资源选择了主题。 然后，单击“选择条件”。 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="“警报”页 -“新建警报规则”按钮":::    
3. 在“配置信号逻辑”页上，执行以下步骤：
    1. 选择指标或活动日志条目。 在此示例中，选择的是“死信事件”。 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="“警报”页 -“新建警报规则”按钮":::        
    2. 选择维度（可选）。 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="“警报”页 -“新建警报规则”按钮":::        

        > [!NOTE]
        > 可以选择 EventSubscriptionName 的 **+** 按钮，以指定用于筛选事件的事件订阅名称。 
    3. 向下滚动。 在“警报逻辑”部分中，选择“运算符”、“聚合类型”，再输入“阈值”，然后选择“完成”    。 在此示例中，当死信事件总计数大于 10 时会触发警报。 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="“警报”页 -“新建警报规则”按钮":::                
4. 返回“创建警报规则”页，单击“选择操作组” 。

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="“警报”页 -“新建警报规则”按钮":::
5. 选择工具栏上的“创建操作组”，以创建一个新操作组。 也可以选择现有的操作组。        
6. 在“添加操作组”页上，执行以下步骤：
    1. 输入操作组的名称。
    1. 为操作组输入“短名称”。
    1. 选择要在其中创建该操作组的“Azure 订阅”。
    1. 选择要在其中创建该操作组的“Azure 资源组”。
    1. 输入操作的名称。 
    1. 选择操作类型。 在此示例中，选择的是“向 Azure 资源管理器角色发送电子邮件”，确切来说是“所有者”角色 。 
    1. 选择“确定”关闭页面。 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="“警报”页 -“新建警报规则”按钮":::                   
7. 返回“创建警报规则”页，输入警报规则的名称，然后选择“创建警报规则” 。

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="“警报”页 -“新建警报规则”按钮":::  
8. 现在，在主题的“警报”页上，可以看到用于管理警报规则的链接（如果尚无警报）。 如果有警报，请选择工具栏上的“管理警报规则”。  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="“警报”页 -“新建警报规则”按钮":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>创建有关其他指标或活动日志操作的警报
上一部分介绍了如何针对死信事件创建警报。 对于创建有关其他指标或活动日志操作的警报，步骤是相似的。 

例如，若要针对交付失败事件创建警报，请在“配置信号逻辑”页上选择“交付失败的事件” 。 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="“警报”页 -“新建警报规则”按钮":::


## <a name="create-alerts-using-the-metrics-page"></a>使用“指标”页创建警报
也可以通过使用“指标”页创建警报。 步骤是相似的。 对于系统主题，只能使用“指标”页创建警报，因为“警报”页面不可用 。 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="“警报”页 -“新建警报规则”按钮":::   
    

> [!NOTE]
> 本文并未涉及创建警报时所有可以使用的不同步骤和组合。 有关警报的概述，请参阅[警报概述](../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>后续步骤

* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
