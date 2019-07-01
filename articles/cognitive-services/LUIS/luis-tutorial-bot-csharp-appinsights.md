---
title: Application Insights，C#
titleSuffix: Azure Cognitive Services
description: 本教程将机器人和语言理解信息添加到 Application Insights 遥测数据存储。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: fa7147dd1b5f22ead17a60042c1c35c4b770cd18
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154915"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>将 LUIS 结果从以 C# 编写的机器人添加到 Application Insights

本教程将机器人和语言理解信息添加到 [Application Insights](https://azure.microsoft.com/services/application-insights/) 遥测数据存储。 有了该数据后，可使用 Kusto 语言或 Power BI 对其进行查询，以便对话语的意向和实体进行实时分析、聚合和报告。 此分析有助于确定是否应添加或编辑 LUIS 应用的意向和实体。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Application Insights 中捕获机器人和语言理解数据
> * 查询 Application Insights 中的语言理解数据

## <a name="prerequisites"></a>先决条件

* 一个 Azure 机器人服务机器人，在创建时已启用 Application Insights。
* 从上一机器人 **[教程](luis-csharp-tutorial-bf-v4.md)** 中下载的机器人代码。 
* [机器人模拟器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

本教程中的所有代码都可在 [Azure-Samples 语言理解 GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry)中找到。 

## <a name="add-application-insights-to-web-app-bot-project"></a>将 Application Insights 添加到 Web 应用机器人项目

目前，此 Web 应用机器人中使用的 Application Insights 服务收集机器人的常规状态遥测数据。 它不收集 LUIS 信息。 

若要捕获 LUIS 信息，需在 Web 应用机器人中安装并配置 **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet 包。  

1. 将依赖项从 Visual Studio 添加到解决方案。 在“解决方案资源管理器”中，右键单击项目名称，然后选择“管理 NuGet 包...”   。NuGet 包管理器随即显示已安装包的列表。 
1. 选择“浏览”，然后搜索 **Microsoft.ApplicationInsights**。 
1. 安装此包。 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>捕获 LUIS 查询结果并发送给 Application Insights

1. 打开 `LuisHelper.cs` 文件并将其内容替换为以下代码。 **LogToApplicationInsights** 方法捕获机器人和 LUIS 数据并将其作为名为 `LUIS` 的跟踪事件发送到 Application Insights。

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>添加 Application Insights 检测密钥 

若要将数据添加到 Application Insights，需提供检测密钥。

1. 在浏览器的 [Azure 门户](https://portal.azure.com)中，找到机器人的 **Application Insights** 资源。 其名称将包含机器人名称的大部分，结尾是随机字符，例如 `luis-csharp-bot-johnsmithxqowom`。 
1. 在 Application Insights 资源的“概览”  页上复制“检测密钥”。 
1. 在 Visual Studio 的机器人项目的根目录中打开 **appsettings.json** 文件。 此文件存储所有环境变量。
1. 添加新变量 `BotDevAppInsightsKey`，其中包含检测密钥的值。 值应当带引号。 

## <a name="build-and-start-the-bot"></a>构建并启动机器人

1. 在 Visual Studio 中构建并运行机器人。 
1. 启动机器人模拟器并打开机器人。 此[步骤](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot)在上一教程中提供。

1. 提问机器人一个问题。 此[步骤](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent)在上一教程中提供。

## <a name="view-luis-entries-in-application-insights"></a>在 Application Insights 中查看 LUIS 条目

打开 Application Insights 以查看 LUIS 条目。 数据显示在 Application Insights 中可能需要数分钟。

1. 在 [Azure 门户](https://portal.azure.com)中，打开机器人的 Application Insights 资源。 
1. 当资源打开以后，选择“搜索”，搜索过去 **30 分钟**内事件类型为“跟踪”的所有数据。   选择名为 **LUIS** 的跟踪。 
1. 机器人和 LUIS 信息在“自定义属性”下提供。  

    ![查看 Application Insights 中存储的 LUIS 自定义属性](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>查询 Application Insights，获取意向、评分和陈述
Application Insights 支持使用 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 语言来查询数据并将其导出到 [Power BI](https://powerbi.microsoft.com)。 

1. 选择“Log Analytics”。  此时将打开一个新窗口，窗口顶部为一个查询窗口，查询窗口下方为一个数据表窗口。 如果之前使用过数据库，则会熟悉这种布局。 此查询代表以前筛选的数据。 **CustomDimensions** 列包含机器人和 LUIS 信息。
1. 若要拉取首要意向、评分和话语，请在查询窗口的最后一行（`|top...` 行）上方添加以下内容：

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. 运行该查询。 此处显示新列“首要意向”、“评分”和“陈述”。 选择“首要意向”列进行排序。

详细了解 [Kusto 查询语言](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)或[将数据导出到 Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)。 


## <a name="learn-more-about-bot-framework"></a>深入了解 Bot Framework

详细了解 [Bot Framework](https://dev.botframework.com/)。

## <a name="next-steps"></a>后续步骤

你可能还希望向 Application Insights 数据中添加其他信息，包括应用 ID、版本 ID、上次模型更改日期、上次训练日期和上次发布日期。 可从终结点 URL（应用 ID 和版本 ID ）或创作 API 调用中检索这些值，然后在 Web 应用机器人设置中对值进行设置并从该位置拉取值。  

如果对多个 LUIS 应用使用同一个终结点订阅，则还应包含订阅 ID 和一个声明此为共享密钥的属性。

> [!div class="nextstepaction"]
> [详细了解示例陈述](luis-how-to-add-example-utterances.md)
