---
title: 适用于认知服务中的文本分析 API 的 C# 快速入门 | Microsoft Docs
description: 获取信息和代码示例，帮助你快速开始使用 Azure 上 Microsoft 认知服务中的文本分析 API。
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: 42a682898303b742a17b0a6d4d98c2b9fedf9003
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841688"
---
# <a name="quickstart-for-the-text-analytics-api-with-c"></a>适用于文本分析 API 的 C# 快速入门 
<a name="HOLTop"></a>

本文展示了如何通过 C# 使用[文本分析 API](//go.microsoft.com/fwlink/?LinkID=759711) 来检测语言、分析情绪和提取关键短语。 代码是为了在 .NET Core 应用程序上工作而编写的，对外部库的引用最少，所以还可以在 Linux 或 MacOS 上运行它。

有关 API 的技术文档，请参阅 [API 定义](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>先决条件

必须拥有包含文本分析 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 可使用“每月 5,000 笔交易的免费层”完成此快速入门。

还必须拥有在注册期间生成的[终结点和访问密钥](../How-tos/text-analytics-how-to-access-key.md)。 


## <a name="install-the-nuget-sdk-package"></a>安装 NuGet SDK 包
1. 在 Visual Studio 中创建一个新的控制台解决方案。
1. 右键单击解决方案，并选择“管理解决方案的 NuGet 包”。
1. 选中“包括预发行版”复选框。
1. 选择“浏览”选项卡，然后搜索“Microsoft.Azure.CognitiveServices.Language”。
1. 选择 Microsoft.Azure.CognitiveServices.Language.TextAnalytics NuGet 包并安装它。

> [!Tip]
> 虽然可以直接通过 C# 调用 [HTTP 终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)，但使用 Microsoft.Azure.CognitiveServices.Language SDK 可以更轻松地调用服务，而不必担心 JSON 的序列化和反序列化。
>
> 下面是一些有用的链接：
> - [SDK NuGet 页面](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK 代码](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-by-using-the-sdk"></a>使用 SDK 调用文本分析 API
1. 将 Program.cs 替换为以下代码： 此程序通过三个部分（语言提取、关键短语提取和情绪分析）演示了文本分析 API 的功能。
1. 将 `Ocp-Apim-Subscription-Key` 标头值替换为对你的订阅有效的访问密钥。
1. 将 `Endpoint` 中的位置替换为你针对其进行注册的终结点。 可以在 Azure 门户资源上找到该终结点。 终结点通常以 "https://[region].api.cognitive.microsoft.com" 开头。 仅包括协议和主机名。
1. 运行该程序。

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;
```

## <a name="detect-language"></a>检测语言

语言检测 API 使用[检测语言方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)检测文本文档的语言。

```csharp
            // Extracting language.
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }
```

## <a name="extract-key-phrases"></a>提取关键短语

关键短语提取 API 使用[关键短语方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)从文本文档中提取关键短语。

```csharp
            // Getting key phrases.
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing key phrases.
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }
```

## <a name="analyze-sentiment"></a>分析情绪

情绪分析 API 使用[情绪方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)检测一组文本记录的情绪。

```csharp
            // Analyzing sentiment.
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results.
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
```

## <a name="identify-linked-entities"></a>识别链接实体

实体链接 API 使用[实体链接方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)识别文本文档中的已知实体。

```csharp
            // Linking entities
            Console.WriteLine("\n\n===== ENTITY LINKING ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                            new MultiLanguageInput("en", "0", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."),
                            new MultiLanguageInput("en", "1", "The Seattle Seahawks won the Super Bowl in 2014."),
                        })).Result;

            // Printing entity results.
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} , Entities: {1}", document.Id, String.Join(", ", document.Entities.Select(entity => entity.Name)));
            }
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另请参阅 

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)
