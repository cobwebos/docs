---
title: "接收有关服务通知的活动日志警报 | Microsoft Docs"
description: "在 Azure 服务发生时，通过短信、电子邮件或 webhook 接收通知。"
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
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 57e136c1027cfa7c789803409cef354f3d52d222
ms.lasthandoff: 03/31/2017


---
# <a name="create-activity-log-alerts-on-service-notifications"></a>创建有关服务通知的活动日志警报
## <a name="overview"></a>概述
本文演示如何使用 Azure 门户为服务运行状况通知设置活动日志警报。  

可以基于针对 Azure 订阅的服务运行状况通知接收警报。  
可以基于以下内容配置警报：
- 服务运行状况通知的类别（事件、维护、 信息等）
- 通知的状态（活动与已解决）
- 通知的级别（信息、警告、错误）

还可以配置应将警报发送到的人员：
- 选择现有操作组
- 创建新操作组（可以在以后用于将来的警报）

可以在[此处](monitoring-action-groups.md)详细了解操作组

可以使用以下方式配置和获取有关服务运行状况通知警报的信息
- [Azure 门户](monitoring-activity-log-alerts-on-service-notifications.md)
- [Resource Manager 模板](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>使用 Azure 门户为新操作组创建有关服务运行状况通知的警报
1.    在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.    单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”部分。

3.    现在单击“警报”部分。

    ![警报](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.    选择“添加活动日志警报”命令，并填写字段

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.    为活动日志警报提供“名称”，并选择“说明”。 这些内容会出现在此警报触发时发送的通知。

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.    “订阅”应自动填充为当前用于操作的订阅。

7.    为此警报选择“资源组”。

8.    在“事件类别”下，选择“服务运行状况”选项。 选择要通知的服务运行状况通知的“类型”、“状态”和“级别”。

9.    通过提供“名称”和“短名称”来“新建”操作组；在此警报触发时发送的通知中会引用短名称。

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

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-with-the-azure-portal"></a>使用 Azure 门户为现有操作组创建有关服务运行状况通知的警报
1.    在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.    单击“监视器”选项打开“监视器”边栏选项卡。 首次打开的是“活动日志”部分。

3.    现在单击“警报”部分。

    ![警报](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.    选择“添加活动日志警报”命令，并填写字段

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.    为活动日志警报提供“名称”，并选择“说明”。 这些内容会出现在此警报触发时发送的通知。

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.    “订阅”应自动填充为当前用于操作的订阅。

7.    为此警报选择“资源组”。

8.    在“事件类别”下，选择“服务运行状况”选项。 选择要通知的服务运行状况通知的“类型”、“状态”和“级别”。

9.    对“通知方式”选择“现有操作组”。 选择相应的操作组。

10.    警报创建完成后，选择“确定”。

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="managing-your-alerts"></a>管理你的警报

创建了警报之后，它会在“监视器”服务的“警报”部分中可见。 选择要管理的警报，你将能够：
* “编辑”它。
* “删除”它。
* 如果要暂时停止或恢复接收警报的通知，可“禁用”或“启用”它。

## <a name="next-steps"></a>后续步骤：
了解[服务运行状况通知](monitoring-service-notifications.md)  
查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md) 获取[活动日志警报概述](monitoring-overview-alerts.md)并了解如何接收警报  
详细了解[操作组](monitoring-action-groups.md)

