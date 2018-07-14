---
title: 使用 C# 将 LUIS 数据添加到 Application Insights | Microsoft Docs
titleSuffix: Azure
description: 使用 C# 构建一个集成有 LUIS 应用程序和 Application Insights 的机器人。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52b6ae224b0e8da12eb4903f5100a6e5cc39704d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "35366959"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>通过 Web 应用机器人将 LUIS 结果添加到 Application Insights
本教程介绍如何将 LUIS 响应信息添加到 [Application Insights](https://azure.microsoft.com/services/application-insights/) 遥测数据存储。 拥有该数据后，可使用 Kusto 语言对其进行查询，或使用 Power BI 对表述的意向和实体进行实时分析、聚合和报告。 此分析有助于确定是否应添加或编辑 LUIS 应用的意向和实体。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
* 将 Application Insights 添加到 Web 应用机器人
* 捕获 LUIS 查询结果并发送给 Application Insights
* 查询 Application Insights，获取首要意向、分数和表述

## <a name="prerequisites"></a>先决条件

* 使用[上一教程](luis-csharp-tutorial-build-bot-framework-sample.md)中已启用 Application Insights 的 LUIS Web 应用机器人。 
* 计算机上已本地安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。

> [!Tip]
> 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/free/)。

本教程中的所有代码均可在 [LUIS 示例 github 存储库](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp)中找到，并且与本教程关联的每行均注释有 `//LUIS Tutorial:`。 

## <a name="review-luis-web-app-bot"></a>检查 LUIS Web 应用机器人
本教程假定你有如下代码或已完成[其他教程](luis-csharp-tutorial-build-bot-framework-sample.md)： 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Web 应用机器人中的 Application Insights
目前，Application Insights 服务被添加到此 Web 应用机器人服务中，可收集该机器人的常规状态遥测数据。 Application Insights 不收集 LUIS 响应信息。 若要分析和改进 LUIS，则需要 LUIS 响应信息。  

若要捕获 LUIS 响应，需在 Web 应用机器人中安装并针对该项目配置 [Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)。 

## <a name="download-web-app-bot"></a>下载 Web 应用机器人
使用 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 为 Web 应用机器人添加和配置 Application Insights。 若要在 Visual Studio 中使用 Web 应用机器人，请下载 Web 应用机器人代码。

1. 在 Azure 门户中，对于 Web 应用机器人，选择“生成”。

    ![在门户中选择“生成”](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. 选择“下载 zip 文件”并等待，直到文件就绪。

    ![下载 zip 文件](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. 在弹出窗口中选择“下载 zip 文件”。 请记住文件在计算机上的位置，下一部分中需要使用该文件。

    ![“下载 zip 文件”弹出窗口](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>在 Visual Studio 2017 中打开解决方案

1. 将文件解压缩到文件夹中。 

2. 打开 Visual Studio 2017，然后打开解决方案文件 `Microsoft.Bot.Sample.LuisBot.sln`。 如果弹出安全警告，请选择“确定”。

    ![在 Visual Studio 2017 中打开解决方案](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio 需要向该解决方案添加依赖项。 在“解决方案资源管理器”中，右键单击“引用”，并选择“管理 NuGet 包...”。 

    ![管理 NuGet 包](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. NuGet 包管理器随即显示已安装包的列表。 选择黄色栏中的“还原”。 等待还原进程完成。

    ![还原 NuGet 包](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>将 Application Insights 添加到项目
在 Visual Studio 中安装和配置 Application Insights。 

1. 在 Visual Studio 2017 的顶部菜单中，选择“项目”，然后选择“添加 Application Insights 遥测...”。

2. 在“Application Insights 配置”窗口中，选择“免费开始”

    ![开始配置 Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. 在 Application Insights 中注册应用。 可能需要输入 Azure 门户凭据。 

4. Visual Studio 会将 Application Insights 添加到项目，并在此过程中显示状态。 

    ![Application Insights 状态](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    该进程完成后，“Application Insights 配置”将显示进度状态。 

    ![Application Insights 进度状态](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    “启用跟踪收集”为红色，表示此功能未启用。 本教程不使用此功能。 

## <a name="build-and-resolve-errors"></a>生成与解决错误

1. 生成解决方案：选择“生成”菜单，然后选择“重新生成解决方案”。 等待生成完成。 

2. 如果生成失败并出现 `CS0104` 错误，则需要进行修复。 在 `Controllers` 文件夹下的 `MessagesController.cs file` 中，对 Activity 类型添加 Connector 类型前缀，解决 `Activity` 类型用法不明确这一问题。 为此，请将 22 行和 36 行的 `Activity` 名称从 `Activity` 更改为 `Connector.Activity`。 再次生成解决方案。 应该不会再出现生成错误。

    该文件的完整源为：

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>将项目发布到 Azure
Application Insights 包现位于该项目中，并已针对 Azure 门户中的凭据进行正确配置。 对该项目的更改需发布回 Azure。

1. 在“解决方案资源管理器”中，右键单击项目名称，然后选择“发布”。

    ![将项目发布到门户](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. 在“发布”窗口中，选择“新建配置文件”。

    ![将项目发布到门户](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. 选择“导入配置文件”，然后选择“确定”。

    ![将项目发布到门户](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. 在“导入发布设置文件”窗口，依次导航到你的项目文件夹和 `PostDeployScripts` 文件夹，选择以 `.PublishSettings` 结尾的文件，然后选择 `Open`。 此项目的发布现已配置完成。 

5. 选择“发布”按钮，将本地源代码发布到机器人服务。 “输出”窗口会显示状态。 本教程的其余部分在 Azure 门户中完成。 关闭 Visual Studio 2017。 

## <a name="open-three-browser-tabs"></a>打开三个浏览器选项卡
在 Azure 门户中，找到 Web 应用机器人并打开。 以下步骤使用 Web 应用机器人的三个不同视图。 在浏览器中打开三个单独的选项卡可能更易于操作： 
  
>  * 通过网页聊天执行测试
>  * 生成/打开联机代码编辑器 -> 应用服务编辑器
>  * 应用服务编辑器/打开 Kudu 控制台 -> 诊断控制台

## <a name="modify-basicluisdialogcs-code"></a>修改 BasicLuisDialog.cs 代码

1. 在“应用服务编辑器”浏览器选项卡中，打开 `BasicLuisDialog.cs` 文件。

2. 在现有 `using` 行下添加以下 NuGet 依赖项：

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. 添加 `LogToApplicationInsights` 函数：

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Application Insights 检测密钥已在 Web 应用机器人的应用程序设置（名为 `BotDevInsightsKey`）中。 

    函数的最后一行会将数据添加到 Application Insights。 此跟踪的名称是 `LUIS`，该名称具有唯一性，与该 Web 应用机器人收集到的任何其他遥测数据均不同。 此外，所有属性具有前缀 `LUIS_`，以便你可以看出本教程在 Web 应用机器人给定信息的基础上添加了哪些数据。

4. 在 `ShowLuisResult` 函数之上调用 `LogToApplicationInsights` 函数：

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>生成 Web 应用机器人
1. 可通过以下两种方法生成 Web 应用机器人。 第一种方法：右键单击“应用服务编辑器”中的 `build.cmd`，然后选择“从控制台运行”。 控制台的输出将显示 `Finished successfully.`，表示已完成

2. 如果未成功完成，则需要打开控制台，导航到此脚本，然后按照以下步骤运行脚本。 在“应用服务编辑器”顶部的蓝色栏中，选择你的机器人的名称，然后在下拉列表中选择“打开 Kudu 控制台”。

    ![打开 Kudu 控制台](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. 在控制台窗口中，输入以下命令：

    ```
    cd site\wwwroot && build.cmd
    ```

    等待生成完成并出现 `Finished successfully.`

## <a name="test-the-web-app-bot"></a>测试 Web 应用机器人

1. 要测试 Web 应用机器人，请在门户中打开“通过网页聊天执行测试”功能。 

2. 输入短语 `Coffee bar on please`。  

    ![通过聊天测试 Web 应用机器人](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. 聊天机器人的响应应该没有区别。 此更改会将数据发送到 Application Insights，而不是包含在机器人响应中。 再输入几个表述，以便略微增加 Application Insights 中的数据：

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>在 Application Insights 中查看 LUIS 条目
打开 Application Insights 以查看 LUIS 条目。 

1. 在门户中，选择“所有资源”，然后按 Web 应用机器人的名称进行筛选。 单击“Application Insights”类型的资源。 Application Insights 的图标是灯泡。 

    ![搜索应用见解](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. 资源打开后，单击最右侧面板中的“搜索”图标（放大镜）。 右侧将显示一个新面板。 该面板可能需要一秒钟才能显示，具体取决于找到的遥测数据量。 搜索 `LUIS`。 该列表已缩减为仅限本教程添加的 LUIS 查询结果。

    ![搜索跟踪](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. 选择最上面的条目。 一个新窗口将显示更详细的数据，包括在最右侧显示 LUIS 查询的自定义数据。 该数据包括首要意图及其分数。

    ![查看跟踪项](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    完成后，选择最右上角的“X”，返回依赖项列表。 


> [!Tip]
> 如果想要保存依赖项列表并稍后回查看，请依次单击“...更多”>“保存收藏”。

## <a name="query-application-insights-for-intent-score-and-utterance"></a>查询 Application Insights，获取意向、分数和表述
Application Insights 支持查询使用 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 语言的数据并将其导出到 [Power BI](https://powerbi.microsoft.com)。 

1. 在筛选框上方，单击依赖项列表顶部的“分析”。 

    ![“分析”按钮](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. 此时将打开一个新窗口，窗口顶部为一个查询窗口，查询窗口下方为一个数据表窗口。 如果之前使用过数据库，则会熟悉这种布局。 该查询包含过去 24 小时内以名称 `LUIS` 开头的所有项。 CustomDimensions 列包含以名称/值对形式显示的 LUIS 查询结果。

    ![默认分析报表](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. 若要拉取首要意向、分数和表述，请在查询窗口的最后一行上方添加以下内容：

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. 运行该查询。 滚动到数据表的最右侧。 此处显示新列“首要意向”、“分数”和“表述”。 单击“首要意向”列进行排序。

    ![自定义分析报表](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


详细了解 [Kusto 查询语言](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries)或[将数据导出到 Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)。 


## <a name="learn-more-about-bot-framework"></a>详细了解 Bot Framework
详细了解 [Bot Framework](https://dev.botframework.com/)。

## <a name="next-steps"></a>后续步骤

你可能还希望向 Application Insights 数据中添加其他信息，包括应用 ID、版本 ID、模型的上次更改日期、上次培训日期和上次发布日期。 可从终结点 URL（应用 ID 和版本 ID ）或[创作 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) 调用中检索这些值，然后在 Web 应用机器人设置中对值进行设置并从该位置拉取值。  

如果对多个 LUIS 应用使用同一个终结点订阅，则还应包含订阅 ID 和一个声明此为共享密钥的属性。 

> [!div class="nextstepaction"]
> [详细了解示例表述](luis-how-to-add-example-utterances.md)
