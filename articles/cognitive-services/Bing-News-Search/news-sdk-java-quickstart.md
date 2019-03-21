---
title: 快速入门：执行新闻搜索 - 适用于 Java 的必应新闻搜索 SDK
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，可以使用适用于 Java 的必应新闻搜索 SDK 搜索新闻，并处理响应。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 648e1701154c725a0645bd520045238030a71ea9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108689"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>快速入门：使用适用于 Java 的必应新闻搜索 SDK 搜索新闻

使用本快速入门开始使用适用于 Java 的必应新闻搜索 SDK 来搜索新闻。 虽然必应新闻搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

使用 Maven、Gradle 或其他依赖项管理系统安装必应新闻搜索 SDK 依赖项。 Maven POM 文件需要以下声明：

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>创建并初始化项目

在你最喜欢的 IDE 或编辑器中新建一个 Java 项目，并导入以下库。

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>创建搜索客户端并存储凭据

1. 创建一个名为 `getClient()` 的方法，用以返回一个新的 `NewsSearchAPIImpl` 搜索客户端。 将你的终结点添加为新的 `NewsSearchAPIImpl` 对象的第一个参数，并添加一个新的 `ServiceClientCredentials` 对象来存储凭据。

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. 若要创建 `ServiceClientCredentials` 对象，请替代 `applyCredentialsFilter()` 函数。 将 `OkHttpClient.Builder` 传递到该方法，并使用 builder 的 `addNetworkInterceptor()` 方法创建用于 SDK 调用的凭据。

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>发送和接收搜索请求

1. 创建一个方法来调用 `getClient()` 并向必应新闻搜索服务发送一个搜索请求。 使用 *market* 和 *count* 参数对搜索进行筛选，然后输出第一个新闻结果的信息：名称、URL、发布日期、说明、提供者名称以及你的搜索的估计匹配项总数。

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. 将搜索方法添加到 `main()` 方法来执行代码。

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](tutorial-bing-news-search-single-page-app.md)
