---
title: 使用 C# 通过 REST 调用获取模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: e4f995e888d261e1a1a7cb1e63d0d222c165060f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368406"
---
## <a name="prerequisites"></a>必备条件

* Azure 语言理解 - 创作资源 32 字符密钥和创作终结点 URL。 使用 [Azure 门户](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或 [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) 创建。
* 从 cognitive-services-language-understanding GitHub 存储库中导入 [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) 应用。
* 导入的 TravelAgent 应用的 LUIS 应用程序 ID。 应用程序仪表板中显示了应用程序 ID。
* 接收言语的应用程序中的版本 ID。 默认 ID 为“0.1”。
* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>以编程方式更改模型

1. 使用项目和名为 `model-with-rest` 的文件夹创建一个面向 C# 语言的新控制台应用程序。

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. 使用以下 dotnet CLI 命令安装所需的依赖项。

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. 将 Program.cs 改写为以下代码：

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your LUIS authoring key - 32 character value
            static string authoringKey = "YOUR-KEY";

            // NOTE: Replace this endpoint with your authoring key endpoint
            // for example, your-resource-name.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";

            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";

            // NOTE: Replace this your version number
            static string appVersion = "0.1";

            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. 将以 `YOUR-` 开头的值替换为你自己的值。

    |信息|目的|
    |--|--|
    |`YOUR-KEY`|32 字符创作密钥。|
    |`YOUR-ENDPOINT`| 创作 URL 终结点。 例如，`replace-with-your-resource-name.api.cognitive.microsoft.com` 。 在创建资源时设置资源名称。|
    |`YOUR-APP-ID`| LUIS 应用 ID。 |

    分配的密钥和资源可以在 LUIS 门户的“Azure 资源”  页上的“管理”部分中看到。 应用 ID 可以在“应用程序设置”  页的同一“管理”部分中找到。

1. 生成控制台应用程序。

    ```console
    dotnet build
    ```

1. 运行控制台应用程序。 控制台输出会显示前面在浏览器窗口中显示的相同 JSON。

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，请从文件系统中删除该文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的最佳实践](../luis-concept-best-practices.md)