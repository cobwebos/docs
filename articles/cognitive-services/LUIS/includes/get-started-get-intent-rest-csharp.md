---
title: 在 C# 中使用 REST 调用获取预测
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 4cbec342bc20de35c0c62284e4e1fe1ae8b8e8a4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966988"
---
## <a name="prerequisites"></a>必备条件

* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：`df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>创建用于预测的 LUIS 运行时密钥

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 单击[创建语言理解  ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 输入运行时密钥的所有必需设置：

    |设置|值|
    |--|--|
    |名称|所需名称（2-64 个字符）|
    |订阅|选择相应的订阅|
    |位置|选择任何附近的可用位置|
    |定价层|`F0` - 最低定价层|
    |资源组|选择可用的资源组|

1. 单击“创建”  并等待创建资源。 创建资源后，导航到资源页。
1. 收集已配置的 `endpoint` 和 `key`。

## <a name="get-intent-programmatically"></a>以编程方式获取意向

使用 C# (.NET Core) 查询[预测终结点](https://aka.ms/luis-apim-v3-prediction)并获取预测结果。

1. 使用项目和名为 `predict-with-rest` 的文件夹创建一个面向 C# 语言的新控制台应用程序。

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. 更改为刚才创建的 `predict-with-rest` 目录，并使用以下命令安装所需的依赖项：

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. 在喜好的 IDE 或编辑器中打开 `Program.cs`。 然后使用以下代码覆盖 `Program.cs`：

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. 将 `YOUR-KEY` 和 `YOUR-ENDPOINT` 值替换为自己的预测密钥和终结点。

    |信息|目的|
    |--|--|
    |`YOUR-KEY`|32 字符预测密钥。|
    |`YOUR-ENDPOINT`| 预测 URL 终结点。 例如，`replace-with-your-resource-name.api.cognitive.microsoft.com` 。|

1. 使用以下命令生成控制台应用程序：

    ```console
    dotnet build
    ```

1. 运行控制台应用程序。 控制台输出会显示前面在浏览器窗口中显示的相同 JSON。

    ```console
    dotnet run
    ```

1. 查看以 JSON 形式返回的预测响应：

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    已通过格式化提高可读性的 JSON 响应：

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，请从文件系统中删除该文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加言语并进行训练](../get-started-get-model-rest-apis.md)