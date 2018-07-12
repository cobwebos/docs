---
title: 集成 QnA Maker 和 LUIS - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 有关集成 QnA Maker 和 LUIS 的分步教程
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 0a0eeb3815b793ed81f60b2b239bc459e5574788
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366304"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>集成 QnA Maker 和 LUIS 以分发知识库
随着 QnA Maker 知识库变得越来越大，将其作为单个整体集进行维护变得困难，并且需要将知识库分成更小的逻辑块。

虽然在 QnA Maker 中创建多个知识库很简单，但你需要一些逻辑来将传入的问题路由到适当的知识库。 可以使用 LUIS 来完成此操作。

## <a name="architecture"></a>体系结构

![QnA Maker luis 体系结构](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

在上面的方案中，QnA Maker 首先从 LUIS 模型获取传入问题的意向，然后使用该意向将问题路由到正确的 QnA Maker 知识库。

## <a name="prerequisites"></a>先决条件
- 登录 [LUIS](https://www.luis.ai/) 门户并[创建应用](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app)。
- 根据方案[添加意向](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents)。
- [训练](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train)并[发布](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) LUIS 应用。
- 登录 [QnA Maker](https://qnamaker.ai)，并根据方案[创建知识库]()。
- [测试]()并[发布]()知识库。

## <a name="qna-maker--luis-bot"></a>QnA Maker + LUIS 机器人
1. 首先使用 LUIS 模板创建 Web 应用机器人，将其与前面创建的 LUIS 应用链接，然后修改意向。 请参阅[此处](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample)的详细步骤。

2. 将依赖项添加到文件顶部，与其他依赖项在一起：

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. 添加以下类以调用 QnA Maker 服务：

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
        public class Metadata
        {
            public string name { get; set; }
            public string value { get; set; }
        }
    
        public class Answer
        {
            public IList<string> questions { get; set; }
            public string answer { get; set; }
            public double score { get; set; }
            public int id { get; set; }
            public string source { get; set; }
            public IList<object> keywords { get; set; }
            public IList<Metadata> metadata { get; set; }
        }
    
        public class QnAAnswer
        {
            public IList<Answer> answers { get; set; }
        }
        /* END - QnA Maker Response Class */
    ```

3. 转到 https://qnamaker.ai ->“我的知识库”-> 查看相应知识库的代码。 获取以下信息：

    ![QnA Maker HTTP 请求](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. 为每个知识库实例化 QnAMakerService 类：
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. 针对意向调用相应的知识库。
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
            }
    ```

## <a name="build-the-bot"></a>生成机器人
1. 在代码编辑器中，右键单击 `build.cmd`，并选择“从控制台运行”。

    ![从控制台运行](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 代码视图将替换为显示生成进度和结果的终端窗口。

    ![控制台生成](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>测试机器人
在 Azure 门户中，选择“在 Web 聊天中测试”以测试机器人。 键入出于不同意向的消息以从相应的知识库获得响应。

![Web 聊天测试](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为 QnA Maker 创建业务连续性计划](../How-To/business-continuity-plan.md)
