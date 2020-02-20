---
title: 使用 Java 通过 REST 调用获取模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 92552a9870f037555a6cde9daa67d3af112ccee7
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368454"
---
## <a name="prerequisites"></a>必备条件

* Azure 语言理解 - 创作资源 32 字符密钥和创作终结点 URL。 使用 [Azure 门户](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或 [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) 创建。
* 从 cognitive-services-language-understanding GitHub 存储库中导入 [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) 应用。
* 导入的 TravelAgent 应用的 LUIS 应用程序 ID。 应用程序仪表板中显示了应用程序 ID。
* 接收言语的应用程序中的版本 ID。 默认 ID 为“0.1”。
* [JDK SE](https://aka.ms/azure-jdks)（Java 开发工具包，标准版）
* [Visual Studio Code](https://code.visualstudio.com/) 或你喜欢用的 IDE

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>以编程方式更改模型

1. 创建一个名为 `lib` 的子目录，并将其复制到以下 Java 库中：

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. 创建名为 `Model.java` 的新文件。 添加以下代码：


    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;

    //javac -cp ":lib/*" Model.java
    //java -cp ":lib/*" Model

    public class Model {

        public static void main(String[] args)
        {
            try
            {

                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "YOUR-APP-ID";

                // Add your endpoint key
                String Key = "YOUR-KEY";

                // Add your endpoint, example is your-resource-name.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";

                String Utterance = "[{'text': 'go to Seattle today','intentName': 'BookFlight','entityLabels': [{'entityName': 'Location::LocationTo',"
                    + "'startCharIndex': 6,'endCharIndex': 12}]},{'text': 'a barking dog is annoying','intentName': 'None','entityLabels': []}]";

                String Version = "1.0";

                // Begin endpoint URL string building
                URIBuilder addUtteranceURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
                URIBuilder trainURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");

                // create URL from string
                URI addUtterancesURI = addUtteranceURL.build();
                URI trainURI = trainURL.build();

                // add utterances POST
                HttpClient addUtterancesClient = HttpClients.createDefault();
                HttpPost addutterancesRequest = new HttpPost(addUtterancesURI);
                addutterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                addutterancesRequest.setHeader("Content-type","application/json");
                HttpResponse addutterancesResponse = addUtterancesClient.execute(addutterancesRequest);
                HttpEntity addutterancesEntity = addutterancesResponse.getEntity();
                if (addutterancesEntity != null)
                {
                    System.out.println(EntityUtils.toString(addutterancesEntity));
                }

                // train POST
                HttpClient trainClient = HttpClients.createDefault();
                HttpPost trainRequest = new HttpPost(trainURI);
                trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainRequest.setHeader("Content-type","application/json");
                HttpResponse trainResponse = trainClient.execute(trainRequest);
                HttpEntity trainEntity = trainResponse.getEntity();
                if (trainEntity != null)
                {
                    System.out.println(EntityUtils.toString(trainEntity));
                }

                // training status GET
                HttpClient trainStatusClient = HttpClients.createDefault();
                HttpGet trainStatusRequest = new HttpGet(trainURI);
                trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainStatusRequest.setHeader("Content-type","application/json");
                HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
                HttpEntity trainStatusEntity = trainStatusResponse.getEntity();
                if (trainStatusEntity != null)
                {
                    System.out.println(EntityUtils.toString(trainStatusEntity));
                }
            }

            catch (Exception e)
            {
                System.out.println(e.getMessage());
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

1. 在创建该文件的同一目录中，在命令提示符下输入以下命令来编译 Java 文件：

    ```console
    javac -cp ":lib/*" Model.java
    ```

1. 通过在命令提示符下输入以下文本从命令行运行 Java 应用程序：

    ```console
    java -cp ":lib/*" Model
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，请从文件系统中删除该文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的最佳实践](../luis-concept-best-practices.md)