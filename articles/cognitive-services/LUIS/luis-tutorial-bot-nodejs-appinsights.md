---
title: 教程：Application Insights，Node.js - LUIS
titleSuffix: Azure Cognitive Services
description: 本教程将机器人和语言理解信息添加到 Application Insights 遥测数据存储。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b0bc4a93df7db7145accf2b485c45256f53c324d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498952"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>教程：将 LUIS 结果从以 Node.js 编写的机器人添加到 Application Insights
本教程将机器人和语言理解信息添加到 [Application Insights](https://azure.microsoft.com/services/application-insights/) 遥测数据存储。 有了该数据后，可使用 Kusto 语言或 Power BI 对其进行查询，以便对话语的意向和实体进行实时分析、聚合和报告。 此分析有助于确定是否应添加或编辑 LUIS 应用的意向和实体。

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Application Insights 中捕获机器人和语言理解数据
> * 查询 Application Insights 中的语言理解数据

## <a name="prerequisites"></a>先决条件

* 一个 Azure 机器人服务机器人，在创建时已启用 Application Insights。
* 从上一机器人 **[教程](luis-nodejs-tutorial-bf-v4.md)** 中下载的机器人代码。 
* [机器人模拟器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

本教程中的所有代码都可在 [Azure-Samples 语言理解 GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry)中找到。 

## <a name="add-application-insights-to-web-app-bot-project"></a>将 Application Insights 添加到 Web 应用机器人项目
目前，此 Web 应用机器人中使用的 Application Insights 服务收集机器人的常规状态遥测数据。 它不收集 LUIS 信息。 

若要捕获 LUIS 信息，需在 Web 应用机器人中安装并配置 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM 包。  

1. 在 VSCode 集成终端的机器人项目的根目录中，使用所示命令添加以下 NPM 包： 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    **underscore** 包用于平展 LUIS JSON 结构，使之在 Application Insights 中更加易于查看和使用。
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>捕获 LUIS 查询结果并发送给 Application Insights

1. 在 VSCode 中创建新文件 **appInsightsLog.js** 并添加以下代码：

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    此文件获取机器人上下文和 LUIS 响应，将两个对象平展后再将其插入 Application Insights 的“跟踪”事件中。  事件的名称为 **LUIS**。 

1. 打开 **dialogs** 文件夹，然后打开 **luisHelper.js** 文件。 包括新的作为必需文件的 **appInsightsLog.js**，并捕获机器人上下文和 LUIS 响应。 此文件的完整代码如下： 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>添加 Application Insights 检测密钥 

若要将数据添加到 Application Insights，需提供检测密钥。

1. 在浏览器的 [Azure 门户](https://portal.azure.com)中，找到机器人的 **Application Insights** 资源。 其名称将包含机器人名称的大部分，结尾是随机字符，例如 `luis-nodejs-bot-johnsmithxqowom`。 
1. 在 Application Insights 资源的“概览”  页上复制“检测密钥”。 
1. 在 VSCode 的机器人项目的根目录中打开 **.env** 文件。 此文件存储所有环境变量。  
1. 添加新变量 `MicrosoftApplicationInsightsInstrumentationKey`，其中包含检测密钥的值。 请勿将值置于引号中。 

## <a name="start-the-bot"></a>启动机器人

1. 从 VSCode 集成终端启动机器人：
    
    ```console
    npm start
    ```

1. 启动机器人模拟器并打开机器人。 此[步骤](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot)在上一教程中提供。

1. 提问机器人一个问题。 此[步骤](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent)在上一教程中提供。

## <a name="view-luis-entries-in-application-insights"></a>在 Application Insights 中查看 LUIS 条目

打开 Application Insights 以查看 LUIS 条目。 数据显示在 Application Insights 中可能需要数分钟。

1. 在 [Azure 门户](https://portal.azure.com)中，打开机器人的 Application Insights 资源。 
1. 当资源打开以后，选择“搜索”，搜索过去 **30 分钟**内事件类型为“跟踪”的所有数据。   选择名为 **LUIS** 的跟踪。 
1. 机器人和 LUIS 信息在“自定义属性”下提供。  

    ![查看 Application Insights 中存储的 LUIS 自定义属性](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>查询 Application Insights，获取意向、评分和陈述
Application Insights 支持使用 [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) 语言来查询数据并将其导出到 [Power BI](https://powerbi.microsoft.com)。 

1. 选择“Log Analytics”。  此时将打开一个新窗口，窗口顶部为一个查询窗口，查询窗口下方为一个数据表窗口。 如果之前使用过数据库，则会熟悉这种布局。 此查询代表以前筛选的数据。 **CustomDimensions** 列包含机器人和 LUIS 信息。
1. 若要拉取首要意向、评分和话语，请在查询窗口的最后一行（`|top...` 行）上方添加以下内容：

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. 运行该查询。 此处显示新列“首要意向”、“评分”和“陈述”。 选择“首要意向”列进行排序。

详细了解 [Kusto 查询语言](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)或[将数据导出到 Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)。 

## <a name="next-steps"></a>后续步骤

你可能还希望向 Application Insights 数据中添加其他信息，包括应用 ID、版本 ID、上次模型更改日期、上次训练日期和上次发布日期。 可从终结点 URL（应用 ID 和版本 ID ）或创作 API 调用中检索这些值，然后在 Web 应用机器人设置中对值进行设置并从该位置拉取值。  

如果对多个 LUIS 应用使用同一个终结点订阅，则还应包含订阅 ID 和一个声明此为共享密钥的属性。 

> [!div class="nextstepaction"]
> [详细了解示例陈述](luis-how-to-add-example-utterances.md)
