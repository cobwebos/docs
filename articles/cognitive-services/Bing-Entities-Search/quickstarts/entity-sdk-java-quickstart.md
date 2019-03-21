---
title: 快速入门：通过适用于 Java 的必应实体搜索 SDK 来搜索实体
titlesuffix: Azure Cognitive Services
description: 使用本快速入门通过适用于 Java 的必应实体搜索 SDK 来搜索实体
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: a5a3bb38f832d13a39a061453e6ef7910ba5cccf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094103"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>快速入门：通过适用于 Java 的必应实体搜索 SDK 来发送搜索请求

使用本快速入门开始通过适用于 Java 的必应实体搜索 SDK 来搜索实体。 虽然必应实体搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

* [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* 适用于 Java 的必应实体搜索 SDK

通过使用 Maven、Gradle 或其他依赖项管理系统安装必应实体搜索 SDK 依赖项。 Maven POM 文件需要以下声明：

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在你最喜欢的 IDE 或编辑器中新建一个 Java 项目，并导入以下库。

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. 为你的订阅密钥创建变量

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>创建搜索客户端

1. 实现 `dominantEntityLookup` 客户端，它需要你的 API 终结点以及 `ServiceClientCredentials` 类的实例。

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    若要实现 `ServiceClientCredentials`，请执行以下步骤：

   1. 重写 `applyCredentialsFilter()` 函数，使用 `OkHttpClient.Builder` 对象作为参数。 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. 在 `applyCredentialsFilter()` 中，调用 `builder.addNetworkInterceptor()`。 创建一个新的 `Interceptor` 对象，并重写其 `intercept()` 方法来获取 `Chain` 侦听器对象。

       ```java
       //...
       builder.addNetworkInterceptor(
           new Interceptor() {
               @Override
               public Response intercept(Chain chain) throws IOException {
               //...    
               }
           });
       ///...
       ```

   3. 在 `intercept` 函数中，为请求创建变量。 使用 `Request.Builder()` 生成请求。 将你的订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头，并返回请求对象上的 `chain.proceed()`。
            
       ```java
       //...
       public Response intercept(Chain chain) throws IOException {
           Request request = null;
           Request original = chain.request();
           Request.Builder requestBuilder = original.newBuilder()
                   .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
           request = requestBuilder.build();
           return chain.proceed(request);
       }
       //...
       ```
      ## <a name="send-a-request-and-receive-a-response"></a>发送请求并接收响应

1. 使用你的订阅密钥创建搜索客户端的新实例。 使用 `client.entities().search()` 发送搜索查询 `satya nadella` 的搜索请求，并获得响应。 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. 如果返回了任何实体，则将它们转换为一个列表。 循环访问它们，并输出主实体。

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建单页 Web 应用](../tutorial-bing-entities-search-single-page-app.md)

* [什么是必应实体搜索 API？](../overview.md )
