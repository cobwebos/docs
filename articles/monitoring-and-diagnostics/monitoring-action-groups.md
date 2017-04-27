---
title: "在 Azure 门户中创建和管理器操作组 | Microsoft Docs"
description: 
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
ms.openlocfilehash: 9a290422bf897397942fccf2e3733d0709701e91
ms.lasthandoff: 03/31/2017


---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>在 Azure 门户中创建和管理器操作组
## <a name="overview"></a>概述 ##
本文演示如何在 Azure 门户中创建和管理操作组。

通过操作组可以配置接收方的列表。 定义活动日志警报时可以利用这些组；确保触发活动日志警报时向特定操作组发送通知。

操作组可以具有最多 10 种不同的操作类型。 操作定义为以下各项的组合：

**名称：**操作组中的唯一标识符。  
**操作类型：**这定义将执行的操作。 选项包括发送短信、发送电子邮件或调用 Webhook。  
**详细信息：**根据操作类型，需要提供对应的电话号码、电子邮件地址或 Webhook URI。

可以使用以下方式配置和获取有关服务运行状况通知警报的信息：
* [Azure 门户](monitoring-action-groups.md)
- [Resource Manager 模板](monitoring-create-action-group-with-resource-manager-template.md)

## <a name="creating-an-action-group-for-the-azure-portal"></a>为 Azure 门户创建操作组 ##
1.    在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-action-groups/home-monitor.png)
2.    单击“监视”选项打开“监视”边栏选项卡。 此边栏选项卡将所有监视设置和数据汇聚到一个合并视图中。 首次打开的是“活动日志”部分。

3.    现在单击“操作组”部分

    ![Action-Group](./media/monitoring-action-groups/action-groups-blade.png)
4.    单击“添加”操作组命令并填写字段

    ![Add-Action-Group](./media/monitoring-action-groups/add-action-group.png)
5.    为操作组提供“名称”和“短名称”；会在发送到此组的通知中引用“短名称”

      ![Action-Group-Define](./media/monitoring-action-groups/action-group-define.png)

6.    “订阅”是在其中保存操作组的订阅。 它会自动填充为当前用于操作的订阅。

7.    为此操作组选择“资源组”。

8.    然后，通过以下各项的租户定义操作的列表：
  1. **名称：**操作组中的唯一标识符。
  2. **操作类型：**这定义将执行的操作。 选项包括发送短信、发送电子邮件或调用 Webhook。
  3. **详细信息：**根据操作类型，需要提供对应的电话号码、电子邮件地址或 Webhook URI。

9.    操作组创建完成后，选择“确定”。

## <a name="managing-your-action-groups"></a>管理操作组 ##
创建了操作组之后，它会在“监视器”服务的“操作组”部分中可见。 选择要管理的操作组，你将能够：
* 添加、编辑或删除接收方。
-    删除操作组。

## <a name="next-steps"></a>后续步骤： ##
详细了解[短信警报行为](monitoring-sms-alert-behavior.md)  
获取[对活动日志警报 webhook 架构的了解](monitoring-activity-log-alerts-webhook.md)  
详细了解有关警报的[速率限制](monitoring-alerts-rate-limiting.md)  
获取[活动日志警报概述](monitoring-overview-alerts.md)，了解如何接收警报  
如何[配置每次发布服务运行状况通知时的警报](monitoring-activity-log-alerts-on-service-notifications.md)

