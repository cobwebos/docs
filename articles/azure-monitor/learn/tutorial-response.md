---
title: 使用 Azure Log Analytics 警报对事件做出响应 | Microsoft Docs
description: 本教程介绍如何通过 Log Analytics 中的警报来确定工作区中的重要信息，并主动将存在的问题通知给你，或者通过调用相关操作来尝试纠正问题。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: 8af1acb67961de105e5bf5900d7b10c437cb30d8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835863"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>借助 Azure Monitor 警报对事件做出响应
Azure Monitor 中的警报可以标识 Log Analytics 存储库中的重要信息。 它们由定期自动运行日志搜索的警报规则创建；如果日志搜索的结果符合特定条件，则创建一条警报记录，并可将此记录配置为执行自动响应。  本教程是[创建和共享 Log Analytics 数据的仪表板](tutorial-logs-dashboards.md)教程的延续。   

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建警报规则
> * 配置用于发送电子邮件通知的操作组

要完成本教程中的示例，必须将现有虚拟机[连接到 Log Analytics 工作区](quick-collect-azurevm.md)。  

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户
在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。 

## <a name="create-alerts"></a>创建警报
警报通过警报规则在 Azure Monitor 中创建，可以按固定的时间间隔自动运行保存的查询或自定义日志搜索。  可按以下条件创建警报：基于特定性能指标、在创建某些事件时、缺少某事件时，或者在特定时间范围内创建大量事件时。  例如，警报可用于在以下情况下通知你：平均 CPU 使用率超过特定阈值、检测到缺少更新，或者在检测到未运行特定 Windows 服务或 Linux 后台程序时生成了某个事件。  如果日志搜索的结果符合特定条件，则会创建警报。 然后，该规则可自动运行一个或多个操作，例如通知你存在警报或调用另一个进程。 

在以下示例中，请根据保存在[可视化数据教程](tutorial-logs-dashboards.md)中的“Azure VM - 处理器利用率”查询创建一条指标度量警报规则。  将会为每个超出阈值 (90%) 的虚拟机创建一条警报。  

1. 在 Azure 门户中，单击“所有服务”  。 在资源列表中，键入“Log Analytics”  。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”  。
2. 在左窗格中选择“警报”，然后单击页面顶部的“新建警报规则”，以便创建新的警报。  <br><br> ![创建新的警报规则](./media/tutorial-response/alert-rule-02.png)<br>
3. 第一步是在“创建警报”部分选择充当资源的 Log Analytics 工作区，  因为这是基于日志的警报信号。  对结果进行筛选，方法是：从下拉列表中选择特定的**订阅**（如果有多个），其中包含此前创建的 VM 和 Log Analytics 工作区。  从下拉列表中选择“Log Analytics”，对“资源类型”进行筛选。    最后，选择**资源** **DefaultLAWorkspace**，然后单击“完成”。 <br><br> ![创建警报步骤 1 任务](./media/tutorial-response/alert-rule-03.png)<br>
4. 在“警报条件”部分单击“添加条件”，   选择保存的查询，然后指定警报规则遵循的逻辑。  在“配置信号逻辑”窗格的列表中选择“Azure VM - 处理器利用率”。    此窗格会进行更新，以呈现警报的配置设置。  在顶部，它会显示所选信号和搜索查询本身在过去 30 分钟的结果。  
5. 使用以下信息配置警报：  
   a. 从“基于”下拉列表中选择“指标度量”   。  指标度量将为查询中其值超出指定阈值的每个对象创建一个警报。  
   b. 选择“大于”作为“条件”，然后输入 **90** 作为“阈值”。    
   c. 在“触发警报的条件”部分选择“连续违规”，然后从下拉列表中选择“大于”并输入 3 作为值。    
   d. 在“评估条件”部分下，将“期间”  值修改为 **30** 分钟。 此规则将每五分钟运行一次，返回从当前时间算起过去 30 分钟内创建的记录。  将时间段设置为更宽的时间窗口可以解决数据延迟的可能性，并确保查询返回数据以避免警报永远不会触发的漏报。  
6. 单击“完成”，完成警报规则。 <br><br> ![配置警报信号](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. 现在转到第二步，在“警报规则名称”字段中提供警报的名称，例如“CPU 百分比大于 90%”。    指定“说明”，详细描述该警报的具体信息，并从提供的选项中选择“关键(严重性 0)”作为“严重性”值。   <br><br> ![配置警报详细信息](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. 若要在创建后立即激活警报规则，请接受“创建后启用规则”选项的默认值。 
9. 第三步也是最后一步，指定“操作组”  ，确保每次触发警报时都执行相同的操作，而且这些操作可以用于定义的每项规则。  使用以下信息配置新操作组：  
   a. 选择“新建操作组”，此时会显示“添加操作组”窗格。    
   b. 对于“操作组名称”  ，请指定一个长名称，例如“IT 操作 - 通知”，以及一个“短名称”，例如“itops-n”。     
   c. 验证“订阅”和“资源组”的默认值是否正确   。 如果否，请从下拉列表中选择正确的值。   
   d. 在“操作”部分指定操作的名称，例如“发送电子邮件”，然后在“操作类型”下的下拉列表中选择“电子邮件/短信/推送/语音”。    “电子邮件/短信/推送/语音”属性窗格会在右侧打开，其中包含更多的信息。   
   e. 在“电子邮件/短信/推送/语音”窗格中启用“电子邮件”，   并提供有效的可以接收邮件的电子邮件 SMTP 地址。  
   f. 单击 **“确定”** 保存所做的更改。<br><br> 

    ![创建新的操作组](./media/tutorial-response/action-group-properties-01.png)

10. 单击“确定”  ，完成操作组。 
11. 单击“创建警报规则”，完成警报规则。  该警报会立即开始运行。<br><br> ![完成新警报规则的创建](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>在 Azure 门户中查看警报
创建警报后，即可在单个窗格中查看 Azure 警报，并可跨 Azure 订阅管理所有警报规则。 此页面列出所有警报规则（已启用的或已禁用的），这些规则可以根据目标资源、资源组、规则名称或状态排序。 包含所有已触发警报和所有已配置/已启用警报规则的聚合摘要。<br><br> ![Azure 警报状态页](./media/tutorial-response/azure-alerts-02.png)  

警报触发时，此表会反映相关条件以及警报在所选时间范围（默认为过去六小时）内发生的次数。  收件箱中会有一封相应的电子邮件，该邮件类似于以下示例，说明了有问题的虚拟机以及在这种情况下与搜索查询最匹配的结果。<br><br> ![警报电子邮件操作示例](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>后续步骤
在本教程中，介绍了警报规则按照计划的时间间隔运行且匹配特定条件时如何主动识别问题并做出响应。

请访问以下链接，查看预生成的 Log Analytics 脚本示例。  

> [!div class="nextstepaction"]
> [Log Analytics 脚本示例](../samples/powershell-samples.md)
