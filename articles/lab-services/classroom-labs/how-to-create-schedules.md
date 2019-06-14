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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60695925"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>在 Azure 实验室服务中创建和管理课堂实验室日程安排 
借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 下面的过程逐步介绍了如何创建和管理课堂实验室日程安排： 

> [!IMPORTANT]
> VM 的计划内运行时间不计入[分配给用户的配额](how-to-configure-student-usage.md#set-quotas-per-user)。 配额是指学生在 VM 上花费的计划外时间。 

## <a name="add-a-schedule-once"></a>添加日程安排（一次性）

1. 切换到“日程安排”  页，再选择工具栏上的“添加日程安排”  。 

    ![“日程安排”页上的“添加日程安排”按钮](../media/how-to-create-schedules/add-schedule-button.png)
2. 在“添加日程安排”  页上，确认是否已选择最上面的“一次性”  选项。 如果没有，请选择“一次性”  。 
3. 对于“日程安排日期(必需)”  ，输入日期，或通过选择日历图标来选择日期。 
4. 对于“启动时间”  ，选择所需 VM 启动时间。 如果未设置停止时间，必须设置启动时间。 如果只想指定停止时间，请选择“删除启动事件”  。 如果“启动时间”  遭禁用，选择下拉列表旁边的“添加启动事件”  即可启用它。 
5. 对于“停止时间”  ，选择所需的 VM 关闭时间。 如果未设置启动时间，必须设置停止时间。 如果只想指定启动时间，请选择“删除停止事件”  。 如果“停止时间”  遭禁用，选择下拉列表旁边的“添加停止事件”  即可启用它。
6. 对于“时区(必需)”  ，选择已指定的启动时间和停止时间的时区。 
7. 对于“备注”  ，输入日程安排的任何说明或备注。 
8. 选择“保存”。  

    ![一次性日程安排](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>添加定期日程安排（每周）

1. 切换到“日程安排”  页，再选择工具栏上的“添加日程安排”  。 

    ![“日程安排”页上的“添加日程安排”按钮](../media/how-to-create-schedules/add-schedule-button.png)
2. 在“添加日程安排”  页上，将最上面的选项切换为“每周”  。 
3. 对于“日程安排日期(必需)”  ，选择所需的日程安排生效日期。 在下面的示例中，选中的是“星期一”至“星期五”。 
4. 对于“开始日期”  字段，输入“日程安排开始日期”  ，或通过选择“日历”  按钮来选择日期。 此字段是必填的。 
5. 对于“日程安排结束日期”  ，输入或选择结束日期（在这一天，VM 关闭）。 
6. 对于“启动时间”  ，选择所需的 VM 启动时间。 如果未设置停止时间，必须设置启动时间。 如果只想指定停止时间，请选择“删除启动事件”  。 如果“启动时间”  遭禁用，选择下拉列表旁边的“添加启动事件”  即可启用它。 
7. 对于“停止时间”  ，选择所需的 VM 关闭时间。 如果未设置启动时间，必须设置停止时间。 如果只想指定启动时间，请选择“删除停止事件”  。 如果“停止时间”  遭禁用，选择下拉列表旁边的“添加停止事件”  即可启用它。
8. 对于“时区(必需)”  ，选择已指定的启动时间和停止时间的时区。  
9. 对于“备注”  ，输入日程安排的任何说明或备注。 
10. 选择“保存”。  

    ![每周日程安排](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>查看日历中的日程安排
可以看到，日历视图中突出显示日程安排日期和时间，如下图所示：

![日历视图中的日程安排](../media/how-to-create-schedules/schedules-in-calendar.png)

选择右上角的“今天”  按钮可以在日历中切换到当前日期。 选择向左箭头  和向右箭头  可以在日历中分别切换到上周和下周。 

## <a name="edit-a-schedule"></a>编辑日程安排
如果双击日历中突出显示的日程安排，或选择工具栏中的铅笔  按钮，便会看到“编辑日程安排”  页。 更新此页上的设置与更新“添加日程安排”  页中的设置（如[添加定期日程安排](#add-a-recurring-schedule-weekly)部分所述）相同。 

![“编辑日程安排”页](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>删除计划

1. 若要删除日程安排，请选择工具栏上的垃圾桶（删除）按钮，如下图所示：

    ![工具栏上的删除按钮](../media/how-to-create-schedules/delete-schedule-button.png)

    可以对日历中的任意日程安排日期和时间使用删除按钮，并选择“删除”  。 
2. 在“删除日程安排”  页上，选择“是”  。

    ![确认是否删除日程安排](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
