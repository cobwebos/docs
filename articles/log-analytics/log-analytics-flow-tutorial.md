---
title: "使用 Microsoft Flow自动执行 Azure Log Analytics 过程"
description: "了解如何通过 Azure Log Analytics 连接器使用 Microsoft Flow 快速自动执行可重复的过程。"
services: log-analytics
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: log-analytics
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 9884b97aad8f022e6bc20b2a630ccdd07ebc64db
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>使用 Microsoft Flow 的连接器自动执行 Log Analytics 过程
[Microsoft Flow](https://ms.flow.microsoft.com) 支持使用数百种操作为不同服务创建自动化工作流。 来自一个操作的输出可以用作另一个操作的输入，从而在不同的服务之间创建集成。  通过用于 Microsoft Flow 的 Azure Log Analytics 连接器，可构建包含 Log Analytics 中日志搜索检索到的数据的工作流。

例如，通过 Microsoft Flow，可在 Office 365 的电子邮件通知中使用 Log Analytics 数据，在 Visual Studio Team Services 中创建 bug，或者发布 Slack 消息。  可通过简单计划或从连接的服务中的某些操作（例如收到电子邮件或推文时）触发工作流。  

本文中的教程展示如何创建自动通过电子邮件发送 Log Analytics 日志搜索结果的流，是关于如何在 Microsoft Flow 中使用 Log Analytics 的示例。 


## <a name="step-1-create-a-flow"></a>步骤 1：创建流
1. 登录 [Microsoft Flow](http://flow.microsoft.com)，选择“我的流”。
2. 单击“从头开始创建”。

## <a name="step-2-create-a-trigger-for-your-flow"></a>步骤 2：为流创建触发器
1. 单击“搜索数百个连接器和触发器”。
2. 在搜索框中输入“计划”。
3. 选择“计划”，然后选择“计划 - 循环”。
4. 在“频率”框中选择“天”，然后在“时间间隔”框中输入“1”。<br><br>![Microsoft Flow 触发器对话框](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>步骤 3：添加 Log Analytics 操作
1. 单击“新建步骤”，然后单击“添加操作”。
2. 搜索“Log Analytics”。
3. 单击“Azure Log Analytics – 运行查询和可视化结果”。<br><br>![Log Analytics 运行查询窗口](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>步骤 4：配置 Log Analytics 操作

1. 指定工作区详细信息，包括订阅 ID，资源组和工作区名称。
2. 将以下 Log Analytics 查询添加到“查询”框中。  这只是一个示例查询，可将它替换为其他任何能返回数据的查询。
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. 在“图表类型”中，选择“Html 表”。<br><br>![Log Analytics 操作](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>步骤 5：配置流以发送电子邮件

1. 单击“新建步骤”，然后单击“+ 添加操作”。
2. 搜索“Office 365 Outlook”。
3. 单击“Office 365 Outlook - 发送电子邮件”。<br><br>![Office 365 Outlook 选择窗口](media/log-analytics-flow-tutorial/flow04.png)

4. 在“收件人”窗口中指定收件人电子邮件地址，在“主题”中指定主题。
5. 单击“正文”框的任何位置。  “动态内容”窗口随即打开，它包含来自之前操作的值。  
6. 选择“正文”。  此处即是 Log Analytics 操作中的查询结果。
6. 单击“显示高级选项”。
7. 在“是否为 HTML”框中选择“是”。<br><br>![Office 365 电子邮件配置窗口](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>步骤 6：保存并测试流
1. 在“流名称”框中为流添加名称，然后单击“创建流”。<br><br>![保存流](media/log-analytics-flow-tutorial/flow06.png)
2. 流现已创建，将在指定的计划日期后运行。 
3. 若要立即测试流，请单击“立即运行”，然后单击“运行流”。<br><br>![运行流](media/log-analytics-flow-tutorial/flow07.png)
3. 流完成时，请检查指定收件人的邮件。  应已收到正文类似以下内容的邮件：<br><br>![示例电子邮件](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>后续步骤

- 详细了解 [Log Analytics 中的日志搜索](log-analytics-log-search-new.md)。
- 详细了解 [Microsoft Flow](https://ms.flow.microsoft.com)。



