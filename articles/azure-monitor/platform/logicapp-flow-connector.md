---
title: 使用 Azure 逻辑应用 Azure Monitor 日志和电源自动化
description: 了解如何使用 Azure 逻辑应用和电源自动化，通过使用 Azure Monitor 连接器快速自动执行可重复的过程。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480005"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>为逻辑应用和流 Azure Monitor 日志连接器
使用[Azure 逻辑应用](/azure/logic-apps/)和[电源自动](https://ms.flow.microsoft.com)操作，可以通过对各种服务使用数百个操作来创建自动化工作流。 使用 "Azure Monitor 日志" 连接器，可以生成在 Azure Monitor 中检索 Log Analytics 工作区或 Application Insights 应用程序中的数据的工作流。 本文介绍连接器附带的操作，并提供了使用此数据生成工作流的演练。

例如，可以创建一个逻辑应用，以便在从 Office 365 发送的电子邮件通知中使用 Azure Monitor 日志数据，在 Azure DevOps 中创建一个 bug，或发布一个可供使用的消息。  可通过简单计划或从连接的服务中的某些操作（例如收到电子邮件或推文时）触发工作流。 

## <a name="actions"></a>操作
下表描述了 Azure Monitor 日志连接器附带的操作。 两者都允许针对 Log Analytics 工作区或 Application Insights 应用程序运行日志查询。 不同之处在于返回数据的方式。

> [!NOTE]
> Azure Monitor 日志连接器替换[Azure Log Analytics 连接器](https://docs.microsoft.com/connectors/azureloganalytics/)和[Azure 应用程序 Insights 连接器](https://docs.microsoft.com/connectors/applicationinsights/)。 此连接器提供与其他连接器相同的功能，并且是针对 Log Analytics 工作区或 Application Insights 应用程序运行查询的首选方法。


| 操作 | 说明 |
|:---|:---|
| [运行查询并列出结果](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | 返回每一行作为其自己的对象。 如果要在工作流的其余部分分别使用每一行，请使用此操作。 [对于每个活动](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)，操作通常后跟。 |
| [运行查询和可视化结果](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 以单个格式化对象的形式返回结果集中的所有行。 如果要在工作流的其余部分一起使用结果集（例如，将结果发送到邮件中），请使用此操作。  |

## <a name="walkthroughs"></a>演练
以下教程说明如何在 Azure 逻辑应用中使用 Azure Monitor 连接器。 可以通过电源自动执行相同的示例，唯一不同之处在于如何创建初始工作流并在完成后运行该工作流。 工作流和操作的配置在两者之间是相同的。 若要开始使用，请参阅[在电源自动执行中通过模板创建流](https://docs.microsoft.com/power-automate/get-started-logic-template)。


### <a name="create-a-logic-app"></a>创建逻辑应用

在 Azure 门户中，单击 "**逻辑应用**"，然后单击 "**添加**"。 选择 "**订阅**"、"**资源组**" 和 "**区域**" 以存储新的逻辑应用，然后为其指定唯一名称。 可以打开**Log Analytics**设置来收集有关运行时数据和事件的信息，如[设置 Azure Monitor 日志和收集 Azure 逻辑应用的诊断数据](../../logic-apps/monitor-logic-apps-log-analytics.md)中所述。 使用 Azure Monitor 日志连接器不需要此设置。

![创建逻辑应用](media/logicapp-flow-connector/create-logic-app.png)


单击 "**查看 + 创建**"，然后单击 "**创建**"。 部署完成后，单击 "**前往资源**" 以打开**逻辑应用设计器**。

### <a name="create-a-trigger-for-the-logic-app"></a>为逻辑应用创建触发器
在 "**使用常见触发器启动**" 下，选择 "**重复周期**"。 这会创建一个自动定期运行的逻辑应用。 在操作的 "**频率**" 框中，选择 "**天**"，然后在 "**间隔**" 框中输入**1** ，以便每天运行一次工作流。

![重复操作](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>演练：邮件可视化结果
以下教程介绍了如何创建逻辑应用，以便通过电子邮件发送 Azure Monitor 日志查询的结果。 

### <a name="add-azure-monitor-logs-action"></a>添加 Azure Monitor 日志操作
单击 " **+ 新建步骤**"，添加在重复执行操作后运行的操作。 在 "**选择操作**" 下，键入 " **azure monitor** "，然后选择 " **Azure Monitor 日志**"。

![Azure Monitor 日志操作](media/logicapp-flow-connector/select-azure-monitor-connector.png)

单击“Azure Log Analytics – 运行查询和可视化结果”****。

![运行查询和可视化结果操作](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>添加 Azure Monitor 日志操作

选择 Log Analytics 工作区的**订阅**和**资源组**。 选择**资源类型** *Log Analytics 工作区*，然后在 "**资源名称**" 下选择工作区的名称。

将以下日志查询添加到“查询”窗口中****。  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

对于 "**时间范围** **" 和 "** **图表类型**"，选择 "*在查询中设置*"。
   
![运行查询和可视化结果操作](media/logicapp-flow-connector/run-query-visualize-action.png)

邮件将由与当前连接相关联的帐户发送。 可以通过单击 "**更改连接**" 来指定其他帐户。

### <a name="add-email-action"></a>添加电子邮件操作

单击 " **+ 新建步骤**"，然后单击 " **+ 添加操作**"。 在 "**选择操作**" 下，键入**outlook** ，然后选择**Office 365 outlook**。

![选择 Outlook connector](media/logicapp-flow-connector/select-outlook-connector.png)

选择 "**发送电子邮件（V2）**"。

![Office 365 Outlook 选择窗口](media/logicapp-flow-connector/select-mail-action.png)

单击 "**正文**" 框中的任意位置，以打开**动态内容**窗口，其中包含逻辑应用中以前操作的值。 选择 "**查看更多**"，然后选择 "**正文**"，这是 Log Analytics 操作中查询的结果。

![选择正文](media/logicapp-flow-connector/select-body.png)

在“收件人”窗口中指定收件人电子邮件地址，在“主题”中指定主题********。 

![邮件操作](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>保存并测试逻辑应用
单击 "**保存**"，然后单击 "**运行**" 以执行逻辑应用的测试运行。

![“保存”和“运行”](media/logicapp-flow-connector/save-run.png)


逻辑应用完成后，检查指定收件人的邮件。  应已收到正文类似以下内容的邮件：

![示例电子邮件](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 中的日志查询](../log-query/log-query-overview.md)。
- 了解有关[逻辑应用](/azure/logic-apps/)的详细信息
- 详细了解 [Microsoft Flow](https://ms.flow.microsoft.com)。

