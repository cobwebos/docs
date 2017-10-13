---
title: "使用 Azure Log Analytics 警报对事件做出响应 | Microsoft Docs"
description: "本教程介绍 Log Analytics 中的警报来帮助你确定 OMS 存储库中的重要信息，并主动通知你存在问题或调用操作来尝试更正问题。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: magoedte
ms.openlocfilehash: ccf0bd57b7f54b1d8d57b460df9bb381b29ee381
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>使用 Log Analytics 警报对事件做出响应
Log Analytics 中的警报标识 Log Analytics 存储库中的重要信息。  它们由定期自动运行日志搜索的警报规则创建；如果日志搜索的结果符合特定条件，则创建一条警报记录，并可将此记录配置为执行自动响应。  本教程是[创建和共享 Log Analytics 数据的仪表板](log-analytics-tutorial-dashboards.md)教程的延续。   

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建警报规则
> * 配置用于发送电子邮件通知的警报规则

要完成本教程中的示例，必须将现有虚拟机[连接到 Log Analytics 工作区](log-analytics-quick-collect-azurevm.md)。  

## <a name="log-in-to-azure-portal"></a>登录到 Azure 门户
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。 

## <a name="create-alerts"></a>创建警报

警报通过警报规则创建，警报规则定期自动运行日志搜索。  可按以下条件创建警报：基于特定性能指标、在创建某些事件时、缺少某事件时，或者在特定时间范围内创建大量事件时。  例如，警报可用于在以下情况时通知你：平均 CPU 使用率超过特定阈值，或者在未运行特定 Windows 服务或 Linux 后台程序时生成某个事件。   如果日志搜索的结果符合特定条件，则会创建警报记录。 然后，该规则可自动运行一个或多个操作以主动通知你存在警报或调用另一个进程。 

下例中，我们创建一个指标度量警报规则，当查询中某个值超过阈值的 90% 时，该规则为每台计算机对象创建一个警报。

1. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 通过选择 OMS 门户启动 OMS 门户，并在“概述”页上选择“日志搜索”。  
3. 从门户顶部选择“收藏夹”，然后在右侧的“保存的搜索”窗格中选择“Azure VM - 处理器使用率”查询。  
4. 单击页面顶部的“**警报**”，以打开“**添加警报规则**”屏幕。  
5. 使用以下信息配置警报规则：  
   a. 提供警报的“名称”，如“VM 处理器使用率超出 90 以上”查询  
   b. 对于“时间窗口”，指定查询的时间范围（例如 30）。  查询仅返回在当前时间的这个范围内创建的记录。  
   c. “警报频率”用于指定应运行查询的频率。  本例中，指定为指定时间范围内的 5 分钟。  
   d.单击“下一步”。 选择“指标度量”，输入 90 作为“聚合值”，并输入 3 作为“触发警报基数”**   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“操作”下禁用电子邮件通知。
6. 单击“**保存**”完成警报规则。 该警报将立即开始运行。<br><br> ![警报规则示例](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

对于 Log Analytics 中警报规则创建的警报记录，其类型为“警报”，SourceSystem 为“OMS”。<br><br> ![生成的警报事件示例](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>警报操作
可使用警报执行高级操作，例如创建电子邮件通知、启动[自动化 Runbook](../automation/automation-runbook-types.md)使用 webhook 在 ITSM 事件管理系统中创建事件记录，或者满足警报条件时以 [IT服务管理连接器解决方案](log-analytics-itsmc-overview.md)作为响应。   

电子邮件操作会将带警报详细信息的电子邮件发送给一位或多位收件人。 可以指定邮件主题，但其内容是由 Log Analytics 构造的标准格式。  让我们更新之前创建的警报规则，并将其配置为通过电子邮件进行通知而不是通过日志搜索主动监视警报记录。     

1. 在 OMS 门户的顶部菜单中，选择“设置”，然后选择“警报”。
2. 从警报规则的列表中，单击之前创建的警报旁边的铅笔图标。
3. 在“操作”部分下启用电子邮件通知。
4. 提供电子邮件的“主题”，例如“处理器使用率超出阈值 90 以上”。
5. 在“收件人”字段中添加一个或多个电子邮件收件人的地址。  如果指定多个地址，则用分号 (;) 分隔地址。
6. 单击“**保存**”完成警报规则。 该警报将立即开始运行。<br><br> ![使用电子邮件通知的警报规则](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>后续步骤
在本教程中，介绍了警报规则按照计划的时间间隔运行且匹配特定条件时如何主动识别问题并做出响应。  

请访问以下链接，查看预生成的 Log Analytics 脚本示例。  

> [!div class="nextstepaction"]
> [Log Analytics 脚本示例](powershell-samples.md)