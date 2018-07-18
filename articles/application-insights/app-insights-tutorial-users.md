---
title: 在 Azure Application Insights 中了解客户 | Microsoft Docs
description: 本教程介绍如何使用 Azure Application Insights 了解客户对应用程序的使用情况。
keywords: ''
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: db61c300ad82270e59d315fa3372d9e4390c7a21
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
ms.locfileid: "24099015"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>使用 Azure Application Insights 了解客户对应用程序的使用情况

Azure Application Insights 收集使用情况信息，帮助了解用户与应用程序的交互情况。  本教程详细介绍可用于分析此信息的不同资源。  将了解如何执行以下操作：

> [!div class="checklist"]
> * 分析有关访问应用程序的用户的详细信息
> * 使用会话信息分析客户对应用程序的使用情况
> * 定义漏斗，以便比较所需用户活动及其实际活动 
> * 创建用于将可视化效果和查询整合到单个文档中的工作簿
> * 对类似用户分组，以便放在一起分析
> * 了解哪些用户会回到应用程序
> * 检查用户如何浏览应用程序


## <a name="prerequisites"></a>先决条件

完成本教程：

- 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 开发
    - Azure 开发
- 下载并安装 [Visual Studio Snapshot Debugger](http://aka.ms/snapshotdebugger)。
- 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](app-insights-asp-net.md)。 
- [从应用程序发送遥测数据](app-insights-usage-overview.md#send-telemetry-from-your-app)，添加自定义事件/页面视图
- 发送[用户上下文](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)，跟踪用户在一段时间内执行的操作及充分利用使用情况功能。

## <a name="log-in-to-azure"></a>登录 Azure
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="get-information-about-your-users"></a>获取有关用户的信息
借助“用户”面板，可使用各种方式了解有关用户的重要详细信息。 此面板可用于了解用户的连接位置、其客户端的详细信息及其所访问的应用程序区域等信息。 

1. 选择“Application Insights”，然后选择订阅。
2. 在菜单中选择“用户”。
3. 默认视图显示过去 24 小时内连接到应用程序的唯一用户的数量。  可更改时间范围并设置各种其他条件来筛选此信息。

    ![查询生成器](media\app-insights-tutorial-users\QueryBuilder.png)

6. 单击“期间”下拉列表，将时间范围更改为 7 天。  此操作可增加面板中不同图表内包含的数据。

    ![更改时间范围](media\app-insights-tutorial-users\TimeRange.png)

4. 单击“拆分方式”下拉列表，向图表添加按用户属性分类的细分。  选择“国家或地区”。  该图表包含的数据相同，但可查看每个国家/地区的用户数的细分。

    ![“国家或地区”图表](media\app-insights-tutorial-users\CountryorRegion.png)

5. 将光标放在图表中的不同条形图上进行查看，请注意，每个国家/地区的计数仅反映由该条形图表示的时间范围。
6. 查看右侧的 **Insights** 列，该列对用户数据执行分析。  此列提供的信息包括：一段时间内的唯一会话的数量，以及具有构成用户数据有效值的常见属性的记录 

    ![Insights 列](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>分析用户会话
“会话”面板与“用户”面板类似。  其中“用户”面板可帮助了解有关访问应用程序的用户的详细信息，“会话”面板可帮助了解这些用户对应用程序的使用情况。  

1. 在菜单中选择“会话”。
2. 查看图表，请注意，用于筛选和细分数据的选项与“用户”面板中的相关选项相同。

    ![会话查询生成器](media\app-insights-tutorial-users\SessionsBuilder.png)

3. 右侧的“这些会话的示例”面板列出包含大量事件的会话。  这些会话是需要分析的重点会话。

    ![这些会话的示例](media\app-insights-tutorial-users\SessionsSample.png)

4. 单击其中一个会话，查看其“会话时间线”，其中显示会话中的每个操作。  此操作可帮助确定具有大量异常的会话等信息。

    ![会话时间线](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>对类似用户进行分组
**队列**是指一组按类似特征进行分组的用户。  可使用队列筛选其他面板中的数据，以便分析特定用户组。  例如，可能只想分析完成了购买的用户。

1.  在菜单中选择“队列”。
2.  单击“新建”，新建队列。
3.  选择“使用者”下拉列表，然后选择一个操作。  仅会包含在报告时间范围内执行此操作的用户。

    ![执行指定操作的用户队列](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  在菜单中选择“用户”。
5.  在“显示”下拉列表中，选择刚才创建的队列。  图表中的数据限制为这些用户。

    ![用户工具中的队列](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>比较所需活动与实际活动
尽管前几个面板侧重于应用程序用户执行的操作，但“漏斗”面板侧重于希望用户执行的操作。  漏斗表示应用程序中的一组步骤和执行这些步骤的用户所占的百分比。  例如，可创建用于度量通过搜索产品连接到应用程序的用户所占百分比的漏斗。  随后可查看将该产品添加到购物车中的用户所占的百分比，以及完成购买的用户所占的百分比。

1. 在菜单中选择“漏斗”，然后单击“新建”。 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. 键入“漏斗名称”。
3. 通过为每个步骤选择一个操作，创建至少具有两个步骤的漏斗。  使用 Application Insights 收集的使用情况数据生成操作列表。

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. 单击“保存”来保存漏斗，然后查看其结果。  漏斗右侧的窗口显示第一个活动之前和最后一个活动之后的最常见事件，帮助了解特定顺序下的用户倾向。

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>了解哪些客户会返回
**保留期**可帮助了解哪些用户会回到应用程序。  

1. 在菜单中选择“保留期”。
2. 默认情况下，经过分析的信息包含执行任何操作后，再返回应用程序执行操作的用户。  例如，可将此筛选器更改为仅包含完成购买后返回应用程序的用户。

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. 针对不同的持续时间以图表和表格的形式显示匹配标准的返回用户。  典型模式为返回用户的数量在一段时间内平稳下降。  从一个时段到下一个时段出现突然下降可能会引起关注。 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>分析用户导航
**用户流**可视化用户在应用程序的页面和功能之间导航的情况。  这有助于回答诸如用户通常从特定页面的哪个位置移动、用户通常如何退出应用程序以及是否存在任何定期重复的操作等问题。

1.  在菜单中选择“用户流”。
2.  单击“新建”来新建用户流，然后单击“编辑”来编辑其详细信息。
3.  将“时间范围”增加为 7 天，然后选择初始事件。  用户流将跟踪从该事件开始的用户会话。

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  此时会显示用户流，可查看不同用户路径及其会话计数。  蓝线指示用户在当前操作后执行的操作。  红线指示用户会话结束。

    ![](media\app-insights-tutorial-users\flows.png)

5.  若要从用户流中删除事件，请单击操作角落中的 **x**，然后单击“创建图形”。  图表会重新绘制，并删除该事件的所有实例。  单击“编辑”查看现在已添加到“已排除的事件”中的事件。

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>整合使用情况数据
**工作簿**可将数据可视化效果、Analytics 查询和文本合并到交互式文档中。  工作簿可用于对常见使用情况信息分组、整合来自特定事件的信息或向团队报告应用程序使用情况。

1.  在菜单中选择“工作簿”。
2.  单击“新建”以新建工作簿。
3.  我们已经提供一个包含前一天的所有使用情况数据（显示为条形图）的查询。  用户可使用此查询、手动编辑此查询，或单击“示例查询”来从其他有用查询中进行选择。

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  单击“完成编辑”。
5.  单击顶部窗格中的“编辑”来编辑工作簿顶部的文本，  其采用 markdown 格式。

    ![](media\app-insights-tutorial-users\markdown.png)

6.  单击“添加用户”，添加包含用户信息的图表。  编辑图表的详细信息（如果需要），然后单击“完成编辑”将其保存。


## <a name="next-steps"></a>后续步骤
现在，你已学会如何分析用户，请继续完成下一教程，学习如何创建将此信息与其他有关应用程序的有用数据合并的自定义仪表板。

> [!div class="nextstepaction"]
> [创建自定义仪表板](app-insights-tutorial-dashboards.md)
