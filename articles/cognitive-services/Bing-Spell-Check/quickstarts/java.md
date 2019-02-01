---
title: 快速入门：必应拼写检查 API，Java
titlesuffix: Azure Cognitive Services
description: 获取信息和代码示例，以帮助你快速开始使用必应拼写检查 API。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: d859d2815f47d753c56002a9484f97149e71f1e4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159242"
---
# <a name="quickstart-for-bing-spell-check-api-with-java"></a>通过 Java 使用必应拼写检查 API 快速入门 

本文展示了如何通过 Java 使用[必应拼写检查 API](https://azure.microsoft.com/services/cognitive-services/spell-check/)。 拼写检查 API 返回它无法识别的单词和建议的替换的列表。 通常，你将向此 API 提交文本，然后在文本中进行建议的替换，或者向应用程序的用户显示这些替换，以便他们可以决定是否进行替换。 本文介绍如何发送包含文本“Hollo, wrld!”的请求。 建议的替换将为“Hello”和“world”。

## <a name="prerequisites"></a>先决条件

需要使用 [JDK 7 或 8](https://aka.ms/azure-jdks) 来编译和运行此代码。 可使用喜欢的 Java IDE（如果有）操作，但文本编辑器足以满足要求。

必须创建一个使用必应拼写检查 API v7 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/#lang)足以满足本快速入门的要求。 需要激活免费试用版时提供的访问密钥，或使用 Azure 仪表板中的付费订阅密钥。  另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="get-spell-check-results"></a>获取拼写检查结果

1. 在喜欢使用的 IDE 中新建一个 Java 项目。
2. 添加下方提供的代码。
3. 使用对订阅有效的访问密钥替换 `subscriptionKey` 值。
4. 运行该程序。

```java
import java.io.*;
import java.net.*;
import javax.net.ssl.HttpsURLConnection;

public class HelloWorld {

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    // NOTE: Replace this example key with a valid subscription key.
    static String key = "ENTER KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";

    public static void check () throws Exception {
        String params = "?mkt=" + mkt + "&mode=" + mode;
        URL url = new URL(host + path + params);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        connection.setRequestProperty("Content-Length", "" + text.length() + 5);
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();

        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            System.out.println(line);
        }
        in.close();
    }

    public static void main(String[] args) {
        try {
            check ();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**响应**

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应拼写检查教程](../tutorials/spellcheck.md)

## <a name="see-also"></a>另请参阅

- [必应拼写检查概述](../proof-text.md)
- [必应拼写检查 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
