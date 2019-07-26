---
title: 快速入门：使用用于 C# 的必应拼写检查 SDK 进行拼写检查
titleSuffix: Azure Cognitive Services
description: 开始使用必应拼写检查 REST API 检查拼写和语法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d98d00275cbd89702e4bae0c93aa262805617e59
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500783"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>快速入门：使用用于 C# 的必应拼写检查 SDK 进行拼写检查

根据此快速入门中的说明开始使用用于 C# 的必应拼写检查 SDK 进行拼写检查。 虽然必应拼写检查具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck) 上找到此示例的源代码。

## <a name="application-dependencies"></a>应用程序依赖项

* 任何版本的 [Visual Studio 2017 或更高版本](https://visualstudio.microsoft.com/downloads/)。
* 必应拼写检查 [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

若要向项目中添加必应拼写检查 SDK，请在 Visual Studio 中的**解决方案资源管理器**中选择“管理 NuGet 包”  选项。 添加 `Microsoft.Azure.CognitiveServices.Language.SpellCheck` 程序包。 此程序包还会安装以下依赖项：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在 Visual Studio 中创建一个新的 C# 控制台解决方案。 然后添加以下 `using` 语句。
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. 创建新类。 然后创建一个名为 `SpellCheckCorrection()` 的异步函数，获取订阅密钥并发送拼写检查请求。

3. 通过创建新 `ApiKeyServiceClientCredentials` 对象来对客户端进行实例化。 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>发送请求并读取响应

1. 在上面创建的函数中，执行以下步骤。 向客户端发送拼写检查请求。 将要检查的文本添加到 `text` 参数中，并将模式设置为 `proof`。  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. 获取第一个拼写检查结果（如果有）。 打印返回的第一个拼写错误的字词（令牌）、令牌类型和建议数字。

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. 获取第一个建议的更正（如果有）。打印建议分数和建议字词。 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
   }

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)