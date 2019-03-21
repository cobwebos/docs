---
title: 快速入门：使用必应视觉搜索 REST API 和 Java 获取图像见解
titleSuffix: Azure Cognitive Services
description: 了解如何将图像上传到必应视觉搜索 API 并获取其相关见解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 79b118c0a4fd28eacf24dc63f04f36314807b41a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531076"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>快速入门：使用必应视觉搜索 REST API 和 Java 获取图像见解

使用本快速入门首次调用必应视觉搜索 API 并查看搜索结果。 此简单 C# 应用程序会将一个图像上传到该 API，并显示返回的相关信息。 虽然此应用程序是使用 Java 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

上传本地图像时，表单数据必须包含 Content-Disposition 标头。 它的 `name` 参数必须设置为“image”，`filename` 参数可以设置为任何字符串。 窗体内容是图像的二进制文件。 可以上传的最大图像大小为 1 MB。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>先决条件

* [Java 开发工具包 (JDK) 7 或 8](https://aka.ms/azure-jdks)
* [Gson 库](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在你最喜欢的 IDE 或编辑器中新建一个 Java 项目，并导入以下库。

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. 为 API 终结点、订阅密钥和图像路径创建变量。 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>创建 JSON 分析程序

通过 `JsonParser` 创建一个方法，使 API 的 JSON 响应更具可读性。

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>构造搜索请求和查询

1. 通过应用程序的主要方法，使用 `HttpClientBuilder.create().build();` 创建 Http 客户端。

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. 创建 `HttpEntity`，将图像上传到 API。

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. 使用终结点创建 `httpPost` 对象，并将标头设置为使用订阅密钥。

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>接收和处理 JSON 响应

1. 使用 `HttpClient.execute()` 向 API 发送请求，并将响应存储在 `InputStream` 对象中。
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. 存储 JSON 字符串，并打印响应。

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](../tutorial-bing-visual-search-single-page-app.md)
