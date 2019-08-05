---
title: 快速入门：使用用于 .NET 的 Azure SDK 和 C# 调用文本分析服务
titleSuffix: Azure Cognitive Services
description: 方便你开始使用文本分析服务和 C# 的信息和代码示例。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: 6bd3907392dad626c1eeb1823c929f1a35d544dd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697674"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>快速入门：使用 .NET SDK 和 C# 调用文本分析服务
<a name="HOLTop"></a>

本快速入门介绍如何使用用于 .NET 的 Azure SDK 和 C# 来分析语言。 虽然[文本分析](//go.microsoft.com/fwlink/?LinkID=759711) REST API 与大多数编程语言兼容，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。

> [!NOTE]
> 为简单起见，本文中的演示代码使用文本分析 .NET SDK 的同步方法。 但是，对于生产场景，建议你在自己的应用程序中使用批处理异步方法，使应用程序保持可缩放且响应迅速。 例如，可以使用 `SentimentBatchAsync` 而不是 `Sentiment`。
>
> 可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics) 上找到此示例的批处理异步版本。

有关技术细节，请查看 SDK for .NET [文本分析参考](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet)。

## <a name="prerequisites"></a>先决条件

* 任何版本的 Visual Studio 2017 或更高版本
* [用于 .NET 的文本分析 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>创建 Visual Studio 解决方案并安装 SDK

1. 创建新的控制台应用 (.NET Core) 项目。 [访问 Visual Studio](https://visualstudio.microsoft.com/vs/)。
1. 右键单击解决方案，并选择“管理解决方案的 NuGet 包”  。
1. 选择“浏览”按钮  。搜索“Microsoft.Azure.CognitiveServices.Language.TextAnalytics”。 

## <a name="authenticate-your-credentials"></a>对凭据进行验证

1. 向 main 类文件（默认为 Program.cs）添加以下 `using` 语句。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. 创建用于存储凭据的新 `ApiKeyServiceClientCredentials` 类，并为每个请求添加此类凭据。

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. 更新 `Program` 类。 为文本分析 API 密钥添加一个常量成员，为服务终结点添加另一个。 记住对文本分析资源使用正确的 Azure 位置。

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```

> [!Tip]
> 若要在生产系统中增强机密的安全性，建议使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)。
>

## <a name="create-a-text-analytics-client"></a>创建文本分析客户端

在项目的 `Main` 函数中，调用需要调用的示例方法。 传递所定义的 `Endpoint` 和 `ApiKey` 参数。

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

以下部分介绍如何调用每项服务功能。

## <a name="perform-sentiment-analysis"></a>执行情绪分析

1. 创建新函数 `SentimentAnalysisExample()`，该函数使用以前创建的客户端。
2. 在同一函数中，调用 `client.Sentiment()` 并获取结果。 如果成功，结果将包含情绪 `Score`，如果不成功，则将包含 `errorMessage`。 评分接近 0 表示消极情绪，评分接近 1 表示积极情绪。

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>输出

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>执行语言检测

1. 创建新函数 `DetectLanguageExample()`，该函数使用以前创建的客户端。
2. 在同一函数中，调用 `client.DetectLanguage()` 并获取结果。 如果成功，结果将包含 `DetectedLanguages` 中检测到的语言列表，如果失败，则将包含 `errorMessage`。 然后输出返回的第一种语言。

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `countryHint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `countryHint = ""` 来重置此参数。

### <a name="output"></a>输出

```console
Language: English
```

## <a name="perform-entity-recognition"></a>执行实体识别

1. 创建新函数 `RecognizeEntitiesExample()`，该函数使用以前创建的客户端。
2. 在同一函数中，调用 `client.Entities()` 并获取结果。 然后循环访问这些结果。 如果成功，结果将包含 `Entities` 中检测到的实体列表，如果失败，则将包含 `errorMessage`。 对于每个检测到的实体，输出其类型、子类型和维基百科名称（如果存在），以及其在原始文本中的位置。

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>输出

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>执行关键短语提取操作

1. 创建新函数 `KeyPhraseExtractionExample()`，该函数使用以前创建的客户端。
2. 在同一函数中，调用 `client.KeyPhrases()` 并获取结果。 如果成功，结果将包含 `KeyPhrases` 中检测到的关键短语列表，如果失败，则将包含 `errorMessage`。 然后输出任何检测到的关键短语。

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>输出

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)


* [文本分析概述](../overview.md)
* [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)
