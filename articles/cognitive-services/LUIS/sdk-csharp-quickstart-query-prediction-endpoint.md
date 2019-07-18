---
title: 快速入门：C# SDK 查询预测终结点
titleSuffix: Azure Cognitive Services
description: 使用 C# SDK 向 LUIS 发送用户话语并接收预测。
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: af20d555a83e8d229ed5d83d3b1d3f242de1e4a8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275807"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>快速入门：使用 C# .NET SDK 查询预测终结点

使用在 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) 上提供的 .NET SDK 向语言理解 (LUIS) 发送用户话语，并接收对用户意向的预测。 

本快速入门将用户话语（例如 `turn on the bedroom light`）发送给公共的语言理解应用程序，然后接收预测并显示在话语中发现的评分最高的意向 `HomeAutomation.TurnOn` 和实体 `HomeAutomation.Room`。 

## <a name="prerequisites"></a>先决条件

* [Visual Studio Community 2017 edition](https://visualstudio.microsoft.com/vs/community/)
* C# 编程语言（包括在 VS Community 2017 中）
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> 完整的解决方案可从 [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) GitHub 存储库获得。

想要更多文档？

 * [SDK 参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>获取认知服务或语言理解密钥

若要将公共应用用于家庭自动化，需要一个对终结点预测有效的密钥。 可以选择对许多认知服务有效的认知服务密钥（在下面使用 Azure CLI 创建），或者选择`Language Understanding`密钥。 

使用以下 [Azure CLI 命令创建认知服务密钥](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)：

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>创建 .NET Core 项目

在 Visual Studio Community 2017 中创建 .NET Core 控制台项目。

1. 打开 Visual Studio Community 2017。
1. 创建新项目，从“Visual C#”部分选择“控制台应用(.NET Core)”。  
1. 输入项目名称 `QueryPrediction`，保留剩余的默认值，然后选择“确定”。 
    这样会使用名为 **Program.cs** 的主代码文件创建简单的项目。

## <a name="add-sdk-with-nuget"></a>添加包含 NuGet 的 SDK

1. 在**解决方案资源管理器**的名为 **QueryPrediction** 的树视图中选择“项目”，然后进行右键单击。 在菜单中选择“管理 NuGet 包...”。 
1. 选择“浏览”，然后输入 `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`。  显示包信息以后，选择“安装”，将包安装到项目中。  
1. 将以下 _using_ 语句添加到 **Program.cs** 顶部。 请勿删除 `System` 的现有 _using_ 语句。 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>创建用于预测的新方法

创建新方法 `GetPrediction`，以便将查询发送到查询预测终结点。 该方法会创建并配置所有必需的对象，然后返回包含 [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) 预测结果的 `Task`。 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>创建凭据对象

将以下代码添加到 `GetPrediction` 方法，以便使用认知服务密钥创建客户端凭据。

将 `<REPLACE-WITH-YOUR-KEY>` 替换为认知服务密钥的区域。 密钥位于 [Azure 门户](https://portal.azure.com)中该资源的“密钥”页上。

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>创建语言理解客户端

在 `GetPrediction` 方法中，在上述代码后面添加下述使用新凭据所需的代码，创建 [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) 客户端对象。 

将 `<REPLACE-WITH-YOUR-KEY-REGION>` 替换为密钥的区域，例如 `westus`。 密钥区域位于 [Azure 门户](https://portal.azure.com)中该资源的“概览”页上。

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>设置查询参数

在 `GetPrediction` 方法中，在上述代码后面添加下述代码，以便设置查询参数。

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>查询预测终结点

在 `GetPrediction` 方法中，在上述代码后面添加下述代码，以便设置查询参数：

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>显示预测结果

更改 **Main** 方法，以便调用新的 `GetPrediction` 方法并返回预测结果：

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>运行项目

在 Studio 中生成项目，然后运行项目以查看查询的输出：

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>后续步骤

详细了解 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) 和 [.NET 参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)。 

> [!div class="nextstepaction"] 
> [教程：生成 LUIS 应用，以确定用户意向](luis-quickstart-intents-only.md) 