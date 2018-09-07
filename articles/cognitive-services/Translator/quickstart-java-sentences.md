---
title: 文本翻译使用 Java 获取句子长度 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本快速入门将在认知服务中使用文本翻译 API 和 Java 找到文本中句子的长度。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 1e68f6b888aa670644ef1e05e5f4f3e26cf70b18
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "43768657"
---
# <a name="quickstart-get-sentence-lengths-with-java"></a>快速入门：使用 Java 获取句子长度

本快速入门使用文本翻译 API 找到文本中句子的长度。

## <a name="prerequisites"></a>先决条件

需要使用 [JDK 7 或 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 来编译和运行此代码。 可使用喜欢的 Java IDE（如果有）操作，但也可以使用文本编辑器。

若要使用文本翻译 API，还需要订阅密钥；请参阅[如何注册文本翻译 API](translator-text-how-to-signup.md)。

## <a name="breaksentence-request"></a>BreakSentence 请求

以下代码使用 [BreakSentence](./reference/v3-0-break-sentence.md) 方法将源文本分解为句子。

1. 在喜欢使用的代码编辑器中新建一个 Java 项目。
2. 添加以下提供的代码。
3. 使用对订阅有效的访问密钥替换 `subscriptionKey` 值。
4. 运行该程序。

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/* NOTE: To compile and run this code:
1. Save this file as BreakSentences.java.
2. Run:
    javac BreakSentences.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar BreakSentences
*/

public class BreakSentences {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsofttranslator.com";
    static String path = "/breaksentence?api-version=3.0";

    static String text = "How are you? I am fine. What did you do today?";

    public static class RequestBody {
        String Text;

        public RequestBody(String text) {
            this.Text = text;
        }
    }

    public static String Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setRequestProperty("X-ClientTraceId", java.util.UUID.randomUUID().toString());
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String BreakSentences () throws Exception {
        URL url = new URL (host + path);

        List<RequestBody> objList = new ArrayList<RequestBody>();
        objList.add(new RequestBody(text));
        String content = new Gson().toJson(objList);

        return Post(url, content);
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = BreakSentences ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

## <a name="breaksentence-response"></a>BreakSentence 响应

在 JSON 中返回成功的响应，如以下示例所示：

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>后续步骤

浏览此快速入门的示例代码和其他内容，包括翻译和音译，以及 GitHub 上的其他示例文本翻译项目。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Java 示例](https://aka.ms/TranslatorGitHub?type=&language=java)
