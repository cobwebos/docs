---
title: 使用 Node.js 将 LUIS 数据添加到 Application Insights | Microsoft Docs
titleSuffix: Azure
description: 使用 Node.js 生成与 LUIS 应用程序和 Application Insights 集成的机器人。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/18/2018
ms.author: v-geberr
ms.openlocfilehash: 929b6e1cc980d7215f91a616820e257aed26bab7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366756"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>通过 Web 应用机器人将 LUIS 结果添加到 Application Insights
本教程将 LUIS 请求和响应信息添加到 [Application Insights](https://azure.microsoft.com/services/application-insights/) 遥测数据存储。 添加该数据后，可使用 Kusto 语言进行查询，或使用 PowerBi 对陈述的意向和实体进行实时分析、聚合和报告。 此分析有助于确定是否应添加或编辑 LUIS 应用的意向和实体。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
* 将 Application Insights 库添加到 Web 应用机器人
* 捕获 LUIS 查询结果并发送给 Application Insights
* 在 Application Insights 中查询首要意向、评分和陈述

## <a name="prerequisites"></a>先决条件

* **[前一教程](luis-nodejs-tutorial-build-bot-framework-sample.md)** 中的 LUIS Web 应用机器人已启用 Application Insights。 

> [!Tip]
> 如果没有订阅，可以注册一个[免费帐户](https://azure.microsoft.com/free/)。

本教程中的所有代码均位于 [LUIS 示例 github 存储库](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs)中，并且与本教程关联的每行均注释有 `//APPINSIGHT:`。 

## <a name="web-app-bot-with-luis"></a>Web 应用机器人与 LUIS
本教程假定你有如下代码或已完成[其他教程](luis-nodejs-tutorial-build-bot-framework-sample.md)： 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>将 Application Insights 库添加到 Web 应用机器人
目前，此 Web 应用机器人中使用的 Application Insights 服务收集机器人的常规状态遥测数据。 它不会收集在检查和修复意向与实体时所需的 LUIS 请求和响应信息。 

若要捕获 LUIS 请求和响应，需在 Web 应用机器人中安装 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM 包，并在 **app.js** 文件中配置该包。 然后，意向对话处理程序需将 LUIS 请求和响应信息发送到 Application Insights。 

1. 在 Azure 门户上的 Web 应用机器人服务中，选择“机器人管理”部分下的“生成”。 

    ![搜索 Application Insights](./media/luis-tutorial-appinsights/build.png)

2. 此时会打开一个包含“应用服务编辑器”的新浏览器标签页。 在顶部栏中选择应用名称，然后选择“打开 Kudu 控制台”。 

    ![搜索 Application Insights](./media/luis-tutorial-appinsights/kudu-console.png)

3. 在控制台中输入以下命令，安装 Application Insights 和 Underscore 包：

    ```
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![搜索 Application Insights](./media/luis-tutorial-appinsights/npm-install.png)

    等待这些包安装完成：

    ```
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Kudu 控制台浏览器标签页中的操作到此完成。

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>捕获 LUIS 查询结果并发送给 Application Insights
1. 在“应用服务编辑器”浏览器选项卡中，打开 **app.js** 文件。

2. 在现有的 `requires` 行下添加以下 NPM 库：

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. 创建 Application Insights 对象，并使用 Web 应用机器人应用程序设置 **BotDevInsightsKey**： 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. 添加 **appInsightsLog** 函数：

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    该函数的最后一行用于将数据添加到 Application Insights。 事件的名称为 **LUIS-results**，这是不同于 Web 应用机器人收集的任何其他遥测数据的唯一名称。 

5. 使用 **appInsightsLog** 函数。 在每个意向对话框中添加该函数：

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. 要测试 Web 应用机器人，请使用“通过网上聊天执行测试”功能。 由于所有工作在 Application Insights 中发生，而不是在机器人响应中发生，因此，不会出现任何差异。

## <a name="view-luis-entries-in-application-insights"></a>在 Application Insights 中查看 LUIS 条目
打开 Application Insights 以查看 LUIS 条目。 

1. 在门户中，选择“所有资源”，然后按 Web 应用机器人的名称进行筛选。 单击类型为“Application Insights”的资源。 Application Insights 的图标是灯泡。 

    ![搜索 Application Insights](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. 资源打开后，单击最右侧面板中的“搜索”图标（放大镜）。 右侧显示一个新面板。 该面板可能需要一秒钟才能显示出来，具体取决于找到的遥测数据量。 搜索 `LUIS-results` 并按 Enter 键。 该列表已缩减为仅限本教程添加的 LUIS 查询结果。

    ![筛选依赖项](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. 选择最上面的条目。 一个新窗口将显示更详细的数据，包括在最右侧显示 LUIS 查询的自定义数据。 该数据包括首要意向及其评分。

    ![依赖项详细信息](./media/luis-tutorial-appinsights/app-insights-detail.png)

    完成后，选择最右上角的“X”，返回依赖项列表。 


> [!Tip]
> 如果想要保存依赖项列表并稍后查看，请依次单击“...更多”、“保存收藏”。

## <a name="query-application-insights-for-intent-score-and-utterance"></a>查询 Application Insights，获取意向、评分和陈述
Application Insights 支持查询使用 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 语言的数据以及将其导出到 [PowerBI](https://powerbi.microsoft.com)。 

1. 在筛选框上方，单击依赖项列表顶部的“分析”。 

    ![“分析”按钮](./media/luis-tutorial-appinsights/analytics-button.png)

2. 顶部将打开一个包含查询窗口的新窗口，下方有一个数据表窗口。 如果之前使用过数据库，则会熟悉这种布局。 该查询包含过去 24 小时内以名称 `LUIS-results` 开头的所有项。 **CustomDimensions** 列以名称/值对的形式显示 LUIS 查询结果。

    ![运行 Analytics 查询窗口](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. 若要拉取首要意向、评分和陈述，请在查询窗口的最后一行上方添加以下内容：

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. 运行该查询。 滚动到数据表的最右侧。 此处显示新列“首要意向”、“评分”和“陈述”。 单击“首要意向”列进行排序。

    ![Analytics 首要意向](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


详细了解 [Kusto 查询语言](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries)或[将数据导出到 PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)。 

## <a name="next-steps"></a>后续步骤

你可能还希望向 Application Insights 数据中添加其他信息，包括应用 ID、版本 ID、上次模型更改日期、上次训练日期和上次发布日期。 可从终结点 URL（应用 ID 和版本 ID ）或[创作 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) 调用中检索这些值，然后在 Web 应用机器人设置中对值进行设置并从该位置拉取值。  

如果对多个 LUIS 应用使用同一个终结点订阅，则还应包含订阅 ID 和声明此为共享密钥属性。 

> [!div class="nextstepaction"]
> [详细了解示例陈述](luis-how-to-add-example-utterances.md)
