---
title: 在 Azure 实验室服务中创建课堂实验室日程安排 | Microsoft Docs
description: 了解如何在 Azure 实验室服务中创建课堂实验室日程安排，以便实验室中的 VM 能够在指定时间启动和关闭。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330554"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>在 Azure 实验室服务中创建和管理课堂实验室日程安排 
借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 下面的过程逐步介绍了如何创建和管理课堂实验室日程安排： 

> [!IMPORTANT]
> VM 的计划内运行时间不计入[分配给用户的配额](how-to-configure-student-usage.md#set-quotas-for-users)。 配额是指学生在 VM 上花费的计划外时间。 

## <a name="set-a-schedule-for-the-lab"></a>为实验室设置计划
为实验室创建计划事件，以便在特定时间自动启动/停止实验室中的 Vm。 前面指定的用户配额是在此计划时间之外分配给每个用户的额外时间。 

1. 切换到 "**计划**" 页，然后选择工具栏上的 "**添加计划事件**"。 

    ![“日程安排”页上的“添加日程安排”按钮](../media/how-to-create-schedules/add-schedule-button.png)
2. 确认 "**标准**" 已选择 "**事件类型**"。 选择 "**仅开始**" 以仅指定 vm 的开始时间。 选择 "**仅停止**" 仅指定 vm 的停止时间。 
7. 在 "**重复**" 部分中，选择当前计划。 

    ![“日程安排”页上的“添加日程安排”按钮](../media/how-to-create-schedules/select-current-schedule.png)
5. 在 "**重复**" 对话框中，执行以下步骤：
    1. 确认为 "**重复**" 字段设置了 "**每周**"。 
    3. 指定**开始日期**。
    4. 指定想要启动 Vm 的**开始时间**。
    5. 指定关闭 Vm 的**停止时间**。 
    6. 指定指定的开始时间和结束时间的**时区**。 
    2. 选择希望计划生效的日期。 在下面的示例中，选择了星期一到星期四。 
    8. 选择“保存”。 

        ![设置重复计划](../media/how-to-create-schedules/set-repeat-schedule.png)

3. 现在，在 "**添加计划事件**" 页上，对于 "**备注（可选）** "，输入计划的任何描述或说明。 
4. 在 "**添加计划事件**" 页上，选择 "**保存**"。 

    ![每周日程安排](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>查看日历中的日程安排
可以看到，日历视图中突出显示日程安排日期和时间，如下图所示：

![日历视图中的日程安排](../media/how-to-create-schedules/schedules-calendar.png)

选择右上角的“今天”按钮可以在日历中切换到当前日期。 选择向左箭头和向右箭头可以在日历中分别切换到上周和下周。 

## <a name="edit-a-schedule"></a>编辑日程安排
当你在日历中选择突出显示的计划时，将看到用于**编辑**或**删除**计划的按钮。 

![“编辑日程安排”页](../media/how-to-create-schedules/schedule-edit-button.png)

在 "**编辑计划事件**" 页上，可以更新计划，并选择 "**保存**"。 

## <a name="delete-a-schedule"></a>删除计划

1. 若要删除计划，请在日历中选择突出显示的计划，并选择垃圾桶图标（删除）按钮：

    ![工具栏上的删除按钮](../media/how-to-create-schedules/schedule-delete-button.png)
2. 在 "**删除计划事件**" 对话框中，选择 **"是"** 以确认删除。 



## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
