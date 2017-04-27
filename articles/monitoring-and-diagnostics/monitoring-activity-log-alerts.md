---
title: "创建活动日志警报 | Microsoft 文档"
description: "在活动日志中发生特定事件时，通过短信、webhook 和电子邮件接收通知。"
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: cbdb1f866c51f2a2622d14e1987660e34cb1b5ff
ms.lasthandoff: 04/03/2017


---
# <a name="create-activity-log-alerts"></a>创建活动日志警报

## <a name="overview"></a>概述
活动日志警报是 Azure 资源，因此可以使用 Azure 门户或 Azure Resource Manager (ARM) 管理它们。 本文演示如何使用 Azure 门户设置有关活动日志事件的警报。

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

还可以配置应将警报发送到的人员：
* 选择现有操作组
- 创建新操作组（可以在以后用于将来的警报）

可以在[此处详细了解操作组](monitoring-action-groups.md)

可以使用以下方式配置和获取有关服务运行状况通知警报的信息
* [Azure 门户](monitoring-activity-log-alerts.md)
- [Resource Manager 模板](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>使用 Azure 门户，通过新操作组创建有关活动日志事件的警报
1.    在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”部分。

3.    现在单击“警报”部分。

    ![警报](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    选择“添加活动日志警报”命令，并填写字段

5.    为活动日志警报提供“名称”，并选择“说明”。 这些内容会出现在此警报触发时发送的通知。

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.    “订阅”应自动填充为当前用于操作的订阅。 这是警报资源将部署到并监视的订阅。

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.    在“订阅”中选择此警报将与之关联的“资源组”。

8.    提供“事件类别”、“资源组”、“资源”、“资源类型”、“操作名称”、“级别”、“状态”和“事件发起者”值，以标识此警报应监视的事件。

9.    通过提供“名称”和“短名称”来“新建”操作组；在此警报激活时发送的通知中会引用短名称。

10.    然后，通过提供接收方来定义接收方的列表

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **名称：**接收方的名称、别名或标识符。

    b.保留“数据库类型”设置，即设置为“共享”。 **操作类型：**选择通过短信、电子邮件或 Webhook 联系接收方

    c. **详细信息：**根据选择的操作类型，提供电话号码、电子邮件地址或 Webhook URI。

11.    警报创建完成后，选择“确定”。

几分钟后，警报将处于活动状态，并按前面所述进行触发。

有关活动日志警报的 webhook 架构的详细信息，请[单击此处](monitoring-activity-log-alerts-webhook.md)

>[!NOTE]
>这些步骤中定义的操作组无法作为现有操作组重复用于所有未来的警报定义。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>使用 Azure 门户为现有操作组创建有关活动日志的警报
1.    在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”部分。

3.    现在单击“警报”部分。

    ![警报](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    选择“添加活动日志警报”命令，并填写字段

5.    为活动日志警报提供“名称”，并选择“说明”。 这些内容会出现在此警报触发时发送的通知。

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.    “订阅”应自动填充为当前用于操作的订阅。

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.    为此警报选择“资源组”。

8.    提供“事件类别”、“资源组”、“资源”、“资源类型”、“操作名称”、“级别”、“状态”和“事件发起者”值，以限定词警报应该应用于的事件的范围。

9.    对“通知方式”选择“现有操作组”。 选择相应的操作组。

10.    警报创建完成后，选择“确定”。

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="managing-your-alerts"></a>管理你的警报

创建了警报之后，它会在“监视器”服务的“警报”部分中可见。 选择要管理的警报，你将能够：
* “编辑”它。
* “删除”它。
* 如果要暂时停止或恢复接收警报的通知，可“禁用”或“启用”它。

## <a name="next-steps"></a>后续步骤：
获取[警报概述](monitoring-overview-alerts.md)  
查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md) 详细了解[操作组](monitoring-action-groups.md)  
了解[服务运行状况通知](monitoring-service-notifications.md)

