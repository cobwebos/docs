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
ms.date: 07/06/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 709e20bc6f0d32509f2cdd4c5a9e69021238dba0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="create-activity-log-alerts"></a>创建活动日志警报

## <a name="overview"></a>概述
活动日志警报是新的活动日志事件与警报中指定的条件匹配时激活的警报。 活动日志警报是 Azure 资源，因此可以使用 Azure 门户或 Azure Resource Manager (ARM) 管理它们。 本文演示如何使用 Azure 门户设置有关活动日志事件的警报。

可以接收有关对订阅中的资源执行的操作的警报或有关可能会影响资源运行状况的服务运行状况事件的警报。 活动日志警报监视警报部署到的特定订阅的活动日志事件。

可以基于以下内容配置警报：
* 事件类别（对于[服务运行状况事件，请单击此处](monitoring-activity-log-alerts-on-service-notifications.md)）
- 资源组
- 资源
- 资源类型
- 操作名称
- 通知的级别（详细、信息、警告、错误、关键）
- 状态
- 事件发起者

>[!NOTE]
>必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。
>
>

还可以配置激活警报时将执行的操作：
* 选择现有操作组
- 创建新操作组（可以在以后用于将来的警报）

可以在[此处详细了解操作组](monitoring-action-groups.md)

可以在[此处](monitoring-activity-log-alerts-on-service-notifications.md)了解用于服务运行状况通知的活动日志警报

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>使用 Azure 门户，通过新操作组创建有关活动日志事件的警报
1.  在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”部分。

3.  现在单击“警报”部分。

    ![警报](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  选择“添加活动日志警报”命令，并填写字段

5.  为活动日志警报提供“名称”，并选择“说明”。

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  **订阅**是在其中保存活动日志警报的订阅。 它会自动填充为当前用于操作的订阅。 这是警报资源将部署到并监视的订阅。

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  在“订阅”中选择此警报将与之关联的“资源组”。

8.  提供“事件类别”、“资源组”、“资源”、“资源类型”、“操作名称”、“级别”、“状态”和“事件发起者”值，以标识此警报应监视的事件。

>[!NOTE]
>必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。
>
>

9.  通过提供“名称”和“短名称”来“新建”操作组；在此警报激活时发送的通知中会引用短名称。

10. 然后，通过提供操作的以下项来定义操作列表

    a. **名称：**操作的名称、别名或标识符。

    b. **操作类型：**选择操作类型：短信、电子邮件或 Webhook

    c. **详细信息：**根据选择的操作类型，提供电话号码、电子邮件地址或 Webhook URI。

11. 警报创建完成后，选择“确定”。

几分钟后，警报将处于活动状态，并按前面所述进行触发。

有关活动日志警报的 webhook 架构的详细信息，请[单击此处](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>这些步骤中定义的操作组无法作为现有操作组重复用于所有未来的警报定义。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>使用 Azure 门户为现有操作组创建有关活动日志的警报
1.  在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”部分。

3.  现在单击“警报”部分。

    ![警报](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  选择“添加活动日志警报”命令，并填写字段

5.  为活动日志警报提供“名称”，并选择“说明”。 这些内容会出现在此警报触发时发送的通知。

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  **订阅**是在其中保存活动日志警报的订阅。 它会自动填充为当前用于操作的订阅。 这是警报资源将部署到并监视的订阅。

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  在“订阅”中选择此警报将与之关联的“资源组”。

8.  提供“事件类别”、“资源组”、“资源”、“资源类型”、“操作名称”、“级别”、“状态”和“事件发起者”值，以限定词警报应该应用于的事件的范围。

9.  对“通知方式”选择“现有操作组”。 选择相应的操作组。

10. 警报创建完成后，选择“确定”。

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="managing-your-alerts"></a>管理你的警报

创建了警报之后，它会在“监视器”服务的“警报”部分中可见。 选择要管理的警报，你将能够：
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

