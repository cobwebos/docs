---
title: 快速入门：适用于 .NET 的文本分析客户端库 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 参考本快速入门开始使用 Azure 认知服务的文本分析 API。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: assafi
ms.openlocfilehash: deb8c742161d59c8926c1ec139978d15b891bd4a
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019476"
---
# <a name="quickstart-text-analytics-client-library-for-net"></a>快速入门：适用于 .NET 的文本分析客户端库
<a name="HOLTop"></a>

适用于 .NET 的文本分析客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 

使用适用于 .NET 的文本分析客户端库执行：

* 情绪分析
* 语言检测
* 实体识别
* 关键短语提取

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE] 
> 为简单起见，本文中的演示代码使用文本分析 .NET SDK 的同步方法。 但是，对于生产场景，建议你在自己的应用程序中使用批处理异步方法，使应用程序保持可缩放且响应迅速。 例如，调用 [SentimentBatchAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) 而不是 [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>设置

### <a name="create-a-text-analytics-azure-resource"></a>创建文本分析 Azure 资源

获取密钥，使 Azure 认知服务能够对你订阅的 Azure 资源表示的应用程序进行身份验证。 在本地计算机上使用 [Azure 门户](../../cognitive-services-apis-create-account.md)或 [Azure CLI](../../cognitive-services-apis-create-account-cli.md) 创建用于文本分析的资源。 还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源

获取试用订阅或资源的密钥后，请为该密钥创建名为 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。 

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `text-analytics quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。 

```console
dotnet new console -n text-analytics-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

在首选的编辑器或 IDE 中，从项目目录打开 program.cs  文件。 然后，添加以下 `using` 指令：

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

在应用程序的 `Main` 方法中，为该资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。 稍后将定义方法。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```csharp
static void Main(string[] args)
{
    // replace this endpoint with the correct one for your Azure resource. 
    string endpoint = $"https://westus.api.cognitive.microsoft.com";
    //This sample assumes you have created an environment variable for your key
    string key = Environment.GetEnvironmentVariable("TEXT_ANALYTICS_SUBSCRIPTION_KEY");

    var credentials = new ApiKeyServiceClientCredentials(key);
    TextAnalyticsClient client = new TextAnalyticsClient(credentials)
    {
        Endpoint = endpoint
    };

    Console.OutputEncoding = System.Text.Encoding.UTF8;
    SentimentAnalysisExample(client);
    // languageDetectionExample(client);
    // RecognizeEntitiesExample(client);
    // KeyPhraseExtractionExample(client);
    Console.ReadLine();
}
```

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的文本分析客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

文本分析客户端是一个 [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) 对象，该对象使用你的密钥在 Azure 中进行身份验证，并提供用于接受文本（单个字符串或批）的函数。 可以同步方式或异步方式将文本发送到 API。 响应对象包含发送的每个文档的分析信息。 


## <a name="code-examples"></a>代码示例

* [对客户端进行身份验证](#authenticate-the-client)
* [情绪分析](#sentiment-analysis)
* [语言检测](#language-detection)
* [实体识别](#entity-recognition)
* [关键短语提取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>验证客户端

创建用于存储凭据的新 `ApiKeyServiceClientCredentials` 类，并将凭据发送到客户端的请求。 在该类中创建 `ProcessHttpRequestAsync()` 的重写，用于将密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

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

在 `main()` 方法中，使用密钥和终结点实例化客户端。

```csharp
var credentials = new ApiKeyServiceClientCredentials(key);
TextAnalyticsClient client = new TextAnalyticsClient(credentials)
{
    Endpoint = endpoint
};
```

## <a name="sentiment-analysis"></a>情绪分析

创建名为 `SentimentAnalysisExample()` 的新函数，用于提取前面创建的客户端，并调用该客户端的 [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) 函数。 如果成功，返回的 [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) 对象将包含情绪 `Score`，否则包含 `errorMessage`。 

评分接近 0 表示消极情绪，评分接近 1 表示积极情绪。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client){
    var result = client.Sentiment("I had the best day of my life.", "en");
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
}
```

### <a name="output"></a>输出

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>语言检测

创建名为 `languageDetectionExample()` 的新函数，用于提取前面创建的客户端，并调用该客户端的 [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函数。 如果成功，返回的 [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) 对象将包含 `DetectedLanguages` 中检测到的语言列表，否则包含 `errorMessage`。  输出返回的第一种语言。

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `countryHint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `countryHint = ""` 来重置此参数。

```csharp
static void languageDetectionExample(TextAnalyticsClient client){
    var result = client.DetectLanguage("This is a document written in English.");
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
}
```
<!--
[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>输出

```console
Language: English
```

## <a name="entity-recognition"></a>实体识别

创建名为 `RecognizeEntitiesExample()` 的新函数，用于提取前面创建的客户端，并调用该客户端的 [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函数。 循环访问这些结果。 如果成功，返回的 [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) 对象将包含 `Entities` 中检测到的实体列表，否则包含 `errorMessage`。 对于每个检测到的实体，输出其类型、子类型和维基百科名称（如果存在），以及其在原始文本中的位置。

```csharp
static void entityRecognitionExample(TextAnalyticsClient client){

    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities){
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches){
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
}
```
<!--
[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

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

## <a name="key-phrase-extraction"></a>关键短语提取

创建名为 `KeyPhraseExtractionExample()` 的新函数，用于提取前面创建的客户端，并调用该客户端的 [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函数。 如果成功，结果将包含 `KeyPhrases` 中检测到的关键短语列表，如果失败，则将包含 `errorMessage`。 输出任何检测到的关键短语。

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

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与资源组相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)


* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
* [检测语言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [语言识别](../how-tos/text-analytics-how-to-language-detection.md)
