---
title: 在 Azure 开发测试实验室中创建实验室的活动日志警报
description: 本文介绍了在 Azure 开发测试实验室中创建实验室的活动日志警报的步骤。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: d5886ea26ddbeb07efc23d61d3197860620eebf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526351"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建实验室的活动日志警报
本文介绍如何在 Azure 开发测试实验室中创建实验室的活动日志警报 (例如：在创建 VM 时或) 删除 VM 时。

## <a name="create-alerts"></a>创建警报
在此示例中，将使用向订阅所有者发送电子邮件的操作为实验室中的所有管理操作创建警报。 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户的搜索栏中，键入 **monitor**，然后从结果列表中选择 " **监视器** "。 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " **新建警报规则** "。 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " **选择资源**"。 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " 的**开发测试实验室**，在列表中选择实验室，然后选择 "**完成**"。

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " **选择条件**"。 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " 页上，选择开发测试实验室支持的信号。 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " 以完成配置条件。 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " **选择操作组**"。 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " 链接。 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " **通过电子邮件发送 Azure 资源管理器角色**) "。 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " 页上，选择角色。 在此示例中，它是 **所有者**。 选择“确定”。 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " 页上，输入警报规则的名称，然后选择 **"确定"**。 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 ":::

## <a name="view-alerts"></a>查看警报 
1. 在此示例中，你将看到有关所有管理操作 (**警报** 的警报) 。 可能需要一段时间才能显示警报。 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 " **警报总数** ") ，你会看到已引发的警报。 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 ":::
1. 如果选择警报，将看到有关它的详细信息。 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 ":::
1. 在此示例中，还会收到一封包含内容的电子邮件，如以下示例中所示： 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="搜索监视器&quot;:::        
1. 选择左侧菜单中的 &quot; **警报** &quot;，然后在工具栏上选择 ":::

## <a name="next-steps"></a>后续步骤
- 若要了解有关使用不同操作类型创建操作组的详细信息，请参阅 [在 Azure 门户中创建和管理操作组](../azure-monitor/platform/action-groups.md)。
- 若要了解有关活动日志的详细信息，请参阅  [Azure 活动日志](../azure-monitor/platform/activity-log.md)。
- 若要了解有关如何设置活动日志警报的信息，请参阅 [活动日志中的警报](../azure-monitor/platform/activity-log-alerts.md)。

