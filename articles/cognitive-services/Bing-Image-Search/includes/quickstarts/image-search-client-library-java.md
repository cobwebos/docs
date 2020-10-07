---
title: 必应图像搜索 Java 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: d7433da99fa1aa607b8c85ce6c8f44adcc6c6f89
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87375643"
---
在本快速入门中，你将使用必应图像搜索客户端库（它是 API 的包装器并包含相同的功能）进行你的第一次图像搜索。 这个简单的 Java 应用程序会发送图像搜索查询、分析 JSON 响应，并显示所返回的第一个图像的 URL。

[GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) 上提供了此示例的源代码以及附加的错误处理和注释。

## <a name="prerequisites"></a>先决条件

最新版的 [Java 开发工具包](https://aka.ms/azure-jdks) (JDK)

通过使用 Maven、Gradle 或其他依赖项管理系统安装必应图像搜索客户端库依赖项。 Maven POM 文件需要以下声明：

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在喜欢的 IDE 或编辑器中新建一个 Java 项目，再向类实现中添加以下导入项：

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. 在 main 方法中，为订阅密钥创建变量，并创建搜索词。 然后，对必应图像搜索客户端进行实例化。

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>向 API 发送搜索请求

1. 使用 `bingImages().search()` 发送包含搜索查询的 HTTP 请求。 将响应保存为 `ImagesModel`。

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>分析并查看结果

分析响应中所返回的图像结果。
如果响应中包含搜索结果，则存储第一个结果并输出其详细信息，例如缩略图 URL、原始 URL 以及返回的图像的总数。  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>另请参阅

* [什么是必应图像搜索？](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [尝试在线互动演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure 认知服务 SDK 的 Java 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Azure 认知服务文档](https://docs.microsoft.com/azure/cognitive-services)
* [必应图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
