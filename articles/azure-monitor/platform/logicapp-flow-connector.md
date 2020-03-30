---
title: 将 Azure 监视器日志与 Azure 逻辑应用一起使用，并自动供电
description: 了解如何使用 Azure 逻辑应用和电源自动功能，使用 Azure 监视器连接器快速自动执行可重复的进程。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480005"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>用于逻辑应用和流的 Azure 监视器日志连接器
[Azure 逻辑应用](/azure/logic-apps/)和[电源自动功能](https://ms.flow.microsoft.com)允许您使用数百个操作为各种服务创建自动化工作流。 Azure 监视器日志连接器允许您构建从日志分析工作区或 Azure 监视器中的应用程序见解应用程序检索数据的工作流。 本文介绍了连接器附带的操作，并提供了使用此数据构建工作流的演练。

例如，您可以创建逻辑应用，用于在 Office 365 的电子邮件通知中使用 Azure 监视器日志数据、在 Azure DevOps 中创建 Bug 或发布 Slack 消息。  可通过简单计划或从连接的服务中的某些操作（例如收到电子邮件或推文时）触发工作流。 

## <a name="actions"></a>操作
下表描述了 Azure 监视器日志连接器中包含的操作。 两者都允许您针对日志分析工作区或应用程序见解应用程序运行日志查询。 区别在于数据的返回方式。

> [!NOTE]
> Azure 监视器日志连接器将替换[Azure 日志分析连接器](https://docs.microsoft.com/connectors/azureloganalytics/)和[Azure 应用程序见解连接器](https://docs.microsoft.com/connectors/applicationinsights/)。 此连接器提供与其他连接器相同的功能，是针对日志分析工作区或应用程序见解应用程序运行查询的首选方法。


| 操作 | 描述 |
|:---|:---|
| [运行查询和列表结果](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | 返回每行作为其自己的对象。 如果要在工作流的其余部分中单独处理每一行，请使用此操作。 操作后跟["对于每个活动](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)"。 |
| [运行查询并可视化结果](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 将结果集中的所有行作为单个格式化对象返回。 如果要在工作流的其余部分（如在邮件中发送结果）中一起使用结果集，请使用此操作。  |

## <a name="walkthroughs"></a>演练
以下教程演示了 Azure 逻辑应用中 Azure 监视器连接器的使用。 您可以使用 Power 自动执行相同的示例，唯一的区别是如何创建初始工作流并在完成后运行它。 工作流和操作的配置在两者之间是相同的。 请参阅[从"电源自动"中的模板创建流](https://docs.microsoft.com/power-automate/get-started-logic-template)以开始。


### <a name="create-a-logic-app"></a>创建逻辑应用

转到 Azure 门户中的**逻辑应用**，然后单击"**添加**"。 选择**订阅**、**资源组**和**区域**以存储新逻辑应用，然后为它指定唯一名称。 可以打开**日志分析**设置以收集有关运行时数据和事件的信息，如[设置 Azure 监视器日志和为 Azure 逻辑应用收集诊断数据](../../logic-apps/monitor-logic-apps-log-analytics.md)所述。 使用 Azure 监视器日志连接器不需要此设置。

![创建逻辑应用](media/logicapp-flow-connector/create-logic-app.png)


单击 **"审阅 + 创建**"，然后**创建**。 部署完成后，单击"**转到资源"** 以打开**逻辑应用设计器**。

### <a name="create-a-trigger-for-the-logic-app"></a>为逻辑应用创建触发器
在 **"从公共触发器开始"** 下，选择 **"重复**"。 这将创建自动以常规时间间隔运行的逻辑应用。 在操作**的"频率"** 框中，选择 **"天"** 和"**间隔"** 框中，输入**1**以每天运行一次工作流。

![重复操作](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>演练：邮件可视化结果
以下教程演示如何创建逻辑应用，该应用通过电子邮件发送 Azure 监视器日志查询的结果。 

### <a name="add-azure-monitor-logs-action"></a>添加 Azure 监视器日志操作
单击 **" 新建步骤**"以添加在重复操作后运行的操作。 在 **"选择操作**"下，键入**azure 监视器**，然后选择**Azure 监视器日志**。

![Azure 监视器日志操作](media/logicapp-flow-connector/select-azure-monitor-connector.png)

单击“Azure Log Analytics – 运行查询和可视化结果”****。

![运行查询并可视化结果操作](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>添加 Azure 监视器日志操作

选择日志分析工作区的**订阅****和资源组**。 为 **"资源类型***"选择日志分析工作区*，然后在 **"资源名称**"下选择工作区的名称。

将以下日志查询添加到“查询”窗口中****。  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

选择"时间**范围**"和"**图表类型**"的**HTML 表***的查询集*。
   
![运行查询并可视化结果操作](media/logicapp-flow-connector/run-query-visualize-action.png)

邮件将由与当前连接关联的帐户发送。 您可以通过单击 **"更改连接**"来指定其他帐户。

### <a name="add-email-action"></a>添加电子邮件操作

单击 **" 新建步骤**"，然后单击 **" 添加操作**"。 在 **"选择操作**"下，键入**Outlook，** 然后选择**Office 365 Outlook**。

![选择 Outlook 连接器](media/logicapp-flow-connector/select-outlook-connector.png)

选择**发送电子邮件 （V2）。**

![Office 365 Outlook 选择窗口](media/logicapp-flow-connector/select-mail-action.png)

单击 **"正文"** 框中的任意位置以打开 **"动态内容**"窗口，该窗口将打开逻辑应用中以前操作的值。 选择 **"查看更多****"，然后选择"在**日志分析"操作中查看查询的结果。然后选择"正文"。

![选择正文](media/logicapp-flow-connector/select-body.png)

在“收件人”窗口中指定收件人电子邮件地址，在“主题”中指定主题********。 

![邮件操作](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>保存并测试逻辑应用
单击 **"保存****"，然后单击"运行"** 以执行逻辑应用的测试运行。

![“保存”和“运行”](media/logicapp-flow-connector/save-run.png)


逻辑应用完成后，请检查指定收件人的邮件。  应已收到正文类似以下内容的邮件：

![示例电子邮件](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 中的日志查询](../log-query/log-query-overview.md)。
- 了解有关[逻辑应用](/azure/logic-apps/)的更多信息
- 详细了解 [Microsoft Flow](https://ms.flow.microsoft.com)。

