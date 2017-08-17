---
title: "创建活动日志警报 | Microsoft 文档"
description: "在活动日志中发生特定事件时，通过短信、webhook 和电子邮件接收通知。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c44ecc0ee61c58ab7590e9a21a5fe002068c6b83
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---
# <a name="create-activity-log-alerts"></a>创建活动日志警报

## <a name="overview"></a>概述
活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。 它们是 Azure 资源，所以可以使用资源管理器模板创建并在 Azure 门户中创建、更新或删除。 本文介绍了活动日志警报背后的概念并展示了如何使用 Azure 门户设置活动日志事件上的警报。

通常出于以下任意一个目的创建活动日志警报：
1. 若要在 Azure 订阅中的资源发生特定更改时接收通知，则通知通常限于特定资源组或资源。 例如，可能会希望在删除 myProductionResourceGroup 中的任何虚拟机时接收通知。 或者，可能会希望在任何新角色分配到订阅中的用户时接收通知。
2. 发生服务运行状况事件时接收通知。 服务运行状况事件包括应用于订阅中资源的事件和维护事件的通知。

在上述任何情况下，活动日志警报只监视在其中创建该警报的订阅中的事件。

可以基于活动日志事件的 JSON 对象中的任何顶层属性配置活动日志警报，但门户只显示最常见的选项：
- **类别** - 管理、服务运行状况、自动缩放和建议中的一项。 [请参阅本文以获取有关活动日志类别的信息](./monitoring-overview-activity-logs.md#categories-in-the-activity-log)并[在此处了解有关服务运行状况事件的详细信息](./monitoring-activity-log-alerts-on-service-notifications.md)。
- **资源组**
- **资源**
- **资源类型**
- **操作名称** - 资源管理器 RBAC 操作名称。
- **级别** - 事件的严重级别（详细、信息、警告、错误、严重）
- **状态** - 事件的状态，通常为“已启动”、“失败”或“成功”
- **事件发起者** - 也称为“调用方”。 电子邮件地址或执行操作的用户的 Active Directory 标识符。

>[!NOTE]
>必须在警报中至少指定上述两项条件之一，其中一项是类别。 不能创建每次在活动日志中创建事件时激活的警报。
>
>

活动日志警报激活后会使用操作组生成操作或通知。 操作组是可重复使用的通知接收方（电子邮件地址、Webhook URL 或 SMS 电话号码），可以从多个警报中引用它来集中和组合通知通道。 可以使用活动日志警报中现有的操作组或在定义活动日志警报时创建新操作组。 可以在[此处详细了解操作组](monitoring-action-groups.md)

可以在[此处](monitoring-activity-log-alerts-on-service-notifications.md)详细了解用于服务运行状况通知的活动日志警报

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>使用 Azure 门户，通过新操作组创建有关活动日志事件的警报
1.  在[门户](https://portal.azure.com)中，导航到“监视”边栏选项卡。

    ![监视](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”边栏选项卡。

3.  现在单击“警报”边栏选项卡。

    ![警报](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  选择“添加活动日志警报”命令，并填写字段。

5.  为活动日志警报提供“名称”，并选择“说明”。

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  所选的“订阅”是在其中保存活动日志警报的订阅。 它自动填充为当前用于工作的订阅。 这是在其中部署警报资源和监视活动日志事件的订阅。

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  选择将在其中创建该警报资源的“资源组”。 请注意，这不是受警报监视的资源组，而是在其中部署警报资源的资源组。

8.  （可选）选择“事件类别”（它修改所显示的其他筛选器集）。 对于管理事件，这包括通过“资源组”、“资源”、“资源类型”、“操作名称”、“级别”、“状态”和“事件发起者”值筛选的选项，以标识此警报应监视的事件。

>[!NOTE]
>必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。
>
>

9.  通过提供“名称”和“短名称”来创建“新”操作组；在此警报激活时发送的通知中会引用短名称。

10. 然后，通过提供操作的以下项来定义操作列表

    a. **名称：**操作的名称、别名或标识符。

    b. **操作类型：**选择操作类型：短信、电子邮件或 Webhook

    c. **详细信息：**根据选择的操作类型，提供电话号码、电子邮件地址或 Webhook URI。

11. 警报创建完成后，选择“确定”。

警报的完全传播需要花费几分钟时间，然后警报才生效并在新事件匹配警报条件时触发警报。

[请参阅本文档以详细了解活动日志警报的 Webhook 架构](monitoring-activity-log-alerts-webhook.md)。

>[!NOTE]
>这些步骤中定义的操作组可以作为现有操作组重复用于所有未来的警报定义。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>使用 Azure 门户为现有操作组创建有关活动日志事件的警报
1.  在[门户](https://portal.azure.com)中，导航到“监视”边栏选项卡。

    ![监视](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”部分。

3.  现在单击“警报”部分。

    ![警报](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  选择“添加活动日志警报”命令，并填写字段。

5.  为活动日志警报提供“名称”，并选择“说明”。 这些内容出现在此警报触发时发送的通知中。

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  所选的“订阅”是在其中保存活动日志警报的订阅。 它自动填充为当前用于工作的订阅。 这是在其中部署警报资源和监视活动日志事件的订阅。

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  选择将在其中创建该警报资源的“资源组”。 请注意，这不是受警报监视的资源组，而是在其中部署警报资源的资源组。

8.  （可选）选择“事件类别”（它修改所显示的其他筛选器集）。 对于管理事件，这包括通过“资源组”、“资源”、“资源类型”、“操作名称”、“级别”、“状态”和“事件发起者”值筛选的选项，以标识此警报应监视的事件。

9.  对“通知方式”选择“现有操作组”。 从该列表中选择现有操作组。

10. 警报创建完成后，选择“确定”。

警报的完全传播需要花费几分钟时间，然后警报才生效并在新事件匹配警报条件时触发警报。

## <a name="managing-your-alerts"></a>管理警报

创建警报之后，可在“监视器”边栏选项卡的“警报”部分查看。 选择要管理的警报，可以：
* “编辑”它。
* “删除”它。
* 如果要暂时停止或恢复接收警报的通知，可“禁用”或“启用”它。

## <a name="next-steps"></a>后续步骤
- 获取[警报概述](monitoring-overview-alerts.md)
- 了解[通知速率限制](monitoring-alerts-rate-limiting.md)
- 查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md)
- 详细了解[操作组](monitoring-action-groups.md)  
- 了解[服务运行状况通知](monitoring-service-notifications.md)
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作。](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

