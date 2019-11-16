---
title: 使用 Java 通过 REST 调用获取意向
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: cac3ee99598f8bcb29bd15c60063159b51f71923
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125419"
---
## <a name="prerequisites"></a>先决条件

* [JDK SE](https://aka.ms/azure-jdks)（Java 开发工具包，标准版）
* [Visual Studio Code](https://code.visualstudio.com/) 或你喜欢用的 IDE
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>以编程方式获取意向

使用 Java 查询预测终结点获取 GET [API](https://aka.ms/luis-apim-v3-prediction) 来获取预测结果。

1. 创建一个名为 `lib` 的子目录，并将其复制到以下 Java 库中：

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. 复制以下代码以在名为 `Predict.java` 的文件中创建一个类：


    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    
    public class Predict {
    
        public static void main(String[] args) 
        {
            HttpClient httpclient = HttpClients.createDefault();
    
            try
            {
    
                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";
                
                // Add your endpoint key 
                String Key = "YOUR-KEY";
    
                // Add your endpoint, example is westus.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";
    
                String Utterance = "turn on all lights";
    
                // Begin endpoint URL string building
                URIBuilder endpointURLbuilder = new URIBuilder("https://" + Endpoint + "/luis/prediction/v3.0/apps/" + AppId + "/slots/production/predict?");
    
                // query string params
                endpointURLbuilder.setParameter("query", Utterance);
                endpointURLbuilder.setParameter("subscription-key", Key);
                endpointURLbuilder.setParameter("show-all-intents", "true");
                endpointURLbuilder.setParameter("verbose", "true");
    
                // create URL from string
                URI endpointURL = endpointURLbuilder.build();
    
                // create HTTP object from URL
                HttpGet request = new HttpGet(endpointURL);
    
                // access LUIS endpoint - analyze text
                HttpResponse response = httpclient.execute(request);
    
                // get response
                HttpEntity entity = response.getEntity();
    
    
                if (entity != null) 
                {
                    System.out.println(EntityUtils.toString(entity));
                }
            }
    
            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }   
    }    
    ```

1. 替换以下值：

    * 将 `YOUR-KEY` 替换为初学者密钥
    * `YOUR-ENDPOINT` 替换为终结点，例如 `westus2.api.cognitive.microsoft.com`


1. 通过命令行编译 Java 程序： 

    ```console
    javac -cp ":lib/*" Predict.java
    ```

1. 通过命令行运行 Java 程序：

    ```console
    java -cp ":lib/*" Predict
    ```

1. 查看 JSON 格式的预测响应：

    ```console
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

## <a name="luis-keys"></a>LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，请从文件系统中删除该文件。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Java 添加话语并进行训练](../get-started-get-model-rest-apis.md)