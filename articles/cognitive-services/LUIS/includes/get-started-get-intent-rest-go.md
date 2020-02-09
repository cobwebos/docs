---
title: 使用 Go 通过 REST 调用获取意向
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 02cb7738e20df6aba8690c9fe2ee718144bad114
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987749"
---
## <a name="prerequisites"></a>必备条件

* [Go](https://golang.org/) 编程语言
* [Visual Studio Code](https://code.visualstudio.com/)
* 公共应用 ID：`df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>创建用于预测的 LUIS 运行时密钥

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 单击[创建语言理解  ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 输入**运行时**密钥的所有必需设置：

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

使用 Go 查询[预测终结点](https://aka.ms/luis-apim-v3-prediction)并获取预测结果。

1. 创建名为 `predict.go` 的新文件。 添加以下代码：

    ```go
    package main

    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {

        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"

        // utterance for public app
        var utterance = "turn on all lights"

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {

        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))

        response, err := http.Get(endpointUrl)

        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }

        response2, err2 := ioutil.ReadAll(response.Body)

        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }

        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. 将 `YOUR-KEY` 和 `YOUR-ENDPOINT` 值替换为自己的预测**运行时**密钥和终结点。

    |信息|目的|
    |--|--|
    |`YOUR-KEY`|32 字符预测**运行时**密钥。|
    |`YOUR-ENDPOINT`| 预测 URL 终结点。 例如，`replace-with-your-resource-name.api.cognitive.microsoft.com` 。|

1. 在创建该文件的同一目录中，在命令提示符下输入以下命令来编译 Go 文件：

    ```console
    go build predict.go
    ```

1. 通过在命令提示符下输入以下文本从命令行运行 Go 应用程序：

    ```console
    go run predict.go
    ```

    命令提示符响应为：

    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON 格式以提高可读性：

    ```json
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