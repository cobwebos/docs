---
title: 创建经典活动日志警报 | Microsoft Docs
description: 在活动日志中发生特定事件时，通过短信、webhook 和电子邮件接收通知。
author: johnkemnetz
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2017
ms.author: johnkem
ms.openlocfilehash: e0d92fcd6d3cbd50a2bcb8166b27b11bc86aad83
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="create-activity-log-alerts-classic"></a>创建活动日志警报（经典）

## <a name="overview"></a>概述
活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。 它们是 Azure 资源，因此，可通过使用 Azure 资源管理器模板进行创建。 此外，还可以在 Azure 门户中创建、更新或删除它们。 本文介绍活动日志警报背后的概念。 然后演示如何使用 Azure 门户设置有关活动日志事件的警报。

> [!NOTE]

>  此过程已替换为新的[警报](monitoring-overview-unified-alerts.md)体验。 本文为以前的体验提供参考。 [了解详细信息](monitoring-activity-log-alerts-new-experience.md)。

通常，你会在以下情况下创建活动日志警报以接收通知：

* 在 Azure 订阅中的资源发生特定更改时，通常限于特定资源组或资源。 例如，可能会希望在删除 myProductionResourceGroup 中的任何虚拟机时接收通知。 或者，可能会希望在任何新角色分配到订阅中的用户时接收通知。
* 发生服务运行状况事件。 服务运行状况事件包括应用于订阅中资源的事件和维护事件的通知。

在上述任何情况下，活动日志警报只监视在其中创建该警报的订阅中的事件。

可以基于活动日志事件的 JSON 对象中的任何顶层属性配置活动日志警报。 但是，门户将显示下面最常用的选项：

- 类别：管理、服务运行状况、自动缩放和建议。 有关详细信息，请参阅 [Azure 活动日志概述](./monitoring-overview-activity-logs.md#categories-in-the-activity-log)。 若要了解有关服务运行状况事件的详细信息，请参阅[接收有关服务通知的活动日志警报](./monitoring-activity-log-alerts-on-service-notifications.md)。
- **资源组**
- **资源**
- **资源类型**
- 操作名称：资源管理器基于角色的访问控制操作名称。
- 级别：事件的严重级别（参考性、信息性、警告、错误或严重）。
- 状态：事件的状态，通常为“已启动”、“已失败”或“已成功”。
- 事件发起者：也称为“调用方”。 电子邮件地址或执行操作的用户的 Azure Active Directory 标识符。

> [!NOTE]
> 当类别是“管理”时，必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。

活动日志警报激活后会使用操作组生成操作或通知。 操作组是一组可重用的通知接收方，例如电子邮件地址、Webhook URL 或短信电话号码。 可以从多个警报中引用接收方，以集中和分组通知通道。 在定义活动日志警报时，有两个选项。 可以：

* 在活动日志警报中使用现有操作组。
* 创建新的操作组。

若要了解有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](monitoring-action-groups.md)。

若要了解有关服务运行状况通知的详细信息，请参阅[接收有关服务运行状况通知的活动日志警报](monitoring-activity-log-alerts-on-service-notifications.md)。

## <a name="create-an-alert-classic-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>使用 Azure 门户，通过新操作组创建有关活动日志事件的警报（经典）
1. 在[门户](https://portal.azure.com)中，选择“监视器”。

    ![“监视”服务](./media/monitoring-activity-log-alerts/home-monitor.png)
2. 在“活动日志”部分，选择“警报（经典）”。

    ![“警报”选项卡](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. 选择“添加活动日志警报”，并填写字段。

4. 在“活动日志警报名称”框中输入一个名称，然后选择“说明”。

    ![“添加活动日志警报”命令](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. “订阅”框会自动填充当前订阅。 此“订阅”是在其中保存操作组的订阅。 警报资源部署到此订阅，并在其中监视活动日志事件。

    ![“添加活动日志警报”对话框](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. 选择在其中创建警报资源的“资源组”。 这不是由警报进行监视的资源组。 相反，它是警报资源所在的资源组。

7. （可选）选择“事件类别”，以修改所显示的其他筛选器。 对于管理事件，筛选器包括“资源组”、“资源”、“资源类型”、“操作名称”、“级别”、“状态”和“事件发起者”。 这些值用于标识此警报应监视哪些事件。

    >[!NOTE]
    >必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。
    >
    >

8. 在“操作组名称”框中输入名称，然后在“短名称”框中输入名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

9.  通过提供操作的以下项来定义操作列表：

    a. 名称：输入操作的名称、别名或标识符。

    b. 操作类型：选择 SMS、电子邮件或 webhook。

    c. 详细信息：根据操作类型，输入电话号码、电子邮件地址或 Webhook URI。

10. 选择“确定”以创建警报。

警报需要几分钟才能完全传播，然后会处于活动状态。 当新事件与警报条件匹配时就会触发此警报。

有关详细信息，请参阅[了解活动日志警报中使用的 webhook 架构](monitoring-activity-log-alerts-webhook.md)。

>[!NOTE]
>这些步骤中定义的操作组可以作为现有操作组重复用于所有未来的警报定义。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>使用 Azure 门户为现有操作组创建有关活动日志事件的警报
1. 执行上一节中的步骤 1 至 7 来创建活动日志警报。

2. 在“通知方式”下，选择“现有”操作组按钮。 从该列表中选择现有操作组。

3. 选择“确定”以创建警报。

警报需要几分钟才能完全传播，然后会处于活动状态。 当新事件与警报条件匹配时就会触发此警报。

## <a name="manage-your-alerts"></a>管理警报

创建警报之后，可在“监视器”边栏选项卡的“警报”部分查看。 选择要管理的警报：

* 编辑它。
* 删除它。
* 如果要暂时停止或恢复接收警报的通知，可“禁用”或“启用”它。

## <a name="next-steps"></a>后续步骤
- 获取[警报概述](monitoring-overview-alerts.md)。
- 了解[通知速率限制](monitoring-alerts-rate-limiting.md)。
- 查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md)。
- 详细了解[操作组](monitoring-action-groups.md)。  
- 了解[服务运行状况通知](monitoring-service-notifications.md)。
- 创建[活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)。
- 创建[活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)。
