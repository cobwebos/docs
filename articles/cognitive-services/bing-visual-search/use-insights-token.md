---
title: 使用见解标记 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 展示了如何将图像的见解标记与必应视觉搜索 API 配合使用来获取有关图像的见解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/05/2019
ms.author: scottwhi
ms.openlocfilehash: e42e56e6361b1fde7ab13655d3c57a90d7235938
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493869"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>使用 insights 标记图像中获取见解

必应视觉搜索 API 将返回有关所提供的图像的信息。 可以通过使用图像的 URL、见解标记或通过上传图像来提供图像。 有关这些选项的信息，请参阅[什么是必应视觉搜索 API？](overview.md)。 本文演示了如何使用见解标记。 有关示例，演示如何上传图像以获取见解，请参阅快速入门教程 ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md)  | [Python](quickstarts/python.md))。

如果图像标记或 URL 发送必应视觉搜索下, 图显示窗体数据必须包括在 POST 的正文中。 窗体数据必须包括`Content-Disposition`标头，并且您必须将其`name`"knowledgeRequest"的参数。 有关详细信息`imageInfo`对象，请参阅请求：

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

本文中的示例展示了如何使用见解标记。 获取从 insights 标记`Image`对象中 /images/搜索 API 响应。 有关获取见解令牌的信息，请参阅[必应图像搜索 API 是什么？](../Bing-Image-Search/overview.md)。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

有关使用见解标记的示例，请参阅 [C#](#use-with-c) | [Java](#use-with-java) | [Node.js](#use-with-nodejs) | [Python](#use-with-python)。

## <a name="use-with-c"></a>使用C#

### <a name="c-prerequisites"></a>C#系统必备组件

- 任何版本的[Visual Studio 2017](https://www.visualstudio.com/downloads/)若要获取在 Windows 上运行此代码。
- Azure 订阅。 对于此快速入门中，你可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)订阅密钥或付费的订阅密钥。

## <a name="run-the-application"></a>运行应用程序

若要运行此应用程序，请执行以下步骤：

1. 在 Visual Studio 中创建的控制台解决方案。
2. 在本快速入门中所示的代码替换 Program.cs 的内容。
3. 将 `accessKey` 值替换为你的订阅密钥。
4. 将 `insightsToken` 值替换为 /图像/搜索响应中的见解标记。
5. 运行该程序。

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>与 Java 配合使用

### <a name="java-prerequisites"></a>Java 系统必备组件

- 必须使用[JDK 7 或 8](https://aka.ms/azure-jdks)编译并运行此代码。 如果已收藏项，但文本编辑器就足够了，您可以使用 Java IDE。
- 对于此快速入门中，你可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)订阅密钥或付费的订阅密钥。

## <a name="run-the-java-application"></a>运行 Java 应用程序

若要运行此应用程序，请执行以下步骤：

1. 下载或安装[Gson Java 库](https://github.com/google/gson)。 你还可以获取 Gson 通过 Maven。
2. 在最喜爱的 IDE 或编辑器中新建一个 Java 项目。
3. 将提供的代码添加到一个名为 `VisualSearch.java` 的文件中。
4. 将 `subscriptionKey` 值替换为你的订阅密钥。
5. 运行该程序。

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>与 Node.js 配合使用

### <a name="nodejs-prerequisites"></a>Node.js 先决条件

- 您必须具有[Node.js 6](https://nodejs.org/en/download/)运行此代码。
- 对于此快速入门中，你可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)订阅密钥或付费的订阅密钥。

## <a name="run-the-javascript-application"></a>运行 JavaScript 应用程序

若要运行此应用程序，请执行以下步骤：

1. 为项目创建一个文件夹（或使用你最喜欢的 IDE 或编辑器）。
2. 从命令提示符或终端导航至刚刚创建的文件夹。
3. 安装请求模块：
  
   ```
   npm install request  
   ```
1. 安装窗体数据模块：  
   ```
   npm install form-data  
   ```
1. 创建一个名为 GetVisualInsights.js 的文件，并将以下代码添加到其中。
1. 将 `subscriptionKey` 值替换为你的订阅密钥。
1. 运行该程序。  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>与 Python 配合使用

### <a name="python-prerequisites"></a>Python 必备组件

- 您必须具有[Python 3](https://www.python.org/)运行此代码。
- 对于此快速入门，可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)订阅密钥或付费订阅密钥。

## <a name="run-the-python-application"></a>运行 Python 应用程序

若要运行此应用程序，请执行以下步骤：

1. 在你最喜欢的 IDE 或编辑器中创建一个新的 Python 项目。
2. 创建一个名为 visualsearch.py 的文件并添加此快速入门中所示的代码。
3. 将 `SUBSCRIPTION_KEY` 值替换为你的订阅密钥。
4. 运行该程序。

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>后续步骤

[创建视觉搜索的单页 web 应用](tutorial-bing-visual-search-single-page-app.md)  
[必应视觉搜索 API 是什么？](overview.md)  
[试用认知服务](https://aka.ms/bingvisualsearchtryforfree)  
[获取免费试用访问密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[映像-视觉搜索](https://aka.ms/bingvisualsearchreferencedoc)
