---
title: 教程：使用必应图像搜索 API 和 C# 提取图像详细信息
titleSuffix: Azure Cognitive Services
description: 使用本文创建可利用必应图像搜索 API 提取图像详细信息的 C# 应用程序。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 995ccc8ffeb436526608803362cc361c74a75316
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790034"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>教程：使用必应图像搜索 API 和 C# 提取图像详细信息

通过必应搜索图像 API 可使用多个[终结点](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint)。 `/details` 终结点接受包含图像的 POST 请求，并可以返回有关该图像的各种详细信息。 此 C# 应用程序使用此 API 发送图像，并显示由必应返回的详细信息（即 JSON 对象），如下所示：

![[JSON 结果]](media/cognitive-services-bing-images-api/jsonResult.jpg)

本教程介绍了如何完成以下操作：

> [!div class="checklist"]
> * 在 `POST` 请求中使用图像搜索 `/details` 终结点
> * 指定请求标头
> * 使用 URL 参数指定结果
> * 上传图像数据并发送 `POST` 请求
> * 将 JSON 结果传输到控制台

该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs) 上获得。

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2017 或更高版本](https://visualstudio.microsoft.com/downloads/)。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>构造图像详细信息搜索请求

以下为 `/details` 终结点，它接受请求正文中包含图像数据的 POST 请求。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

在构造搜索请求 URL 时，`modules` 参数遵循上述终结点并指定结果将包含的详细信息类型：

* `modules=All`
* `modules=RecognizedEntities`（图像中可见的人物或地点）

在 POST 请求中指定 `modules=All` 以获取包含以下内容的 JSON 文本：

* `bestRepresentativeQuery` - 返回与上传图像类似图像的必应查询
* `detectedObjects` - 图像中找到的对象
* `image` - 图像的元数据
* `imageInsightsToken` - 之后从图像中获取 `RecognizedEntities`（图像中可见的人物或地点）的 GET 请求的令牌
* `imageTags` - 图像的标记
* `pagesIncluding` - 包括该图像的网页
* `relatedSearches` - 基于图像中的详细信息的搜索。
* `visuallySimilarImages` - Web 上类似的图像。

在 POST 请求中指定 `modules=RecognizedEntities` 以仅获取 `imageInsightsToken`，其可用于随后的 GET 请求中以识别图像中的人物或地点。

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>创建一个 WebClient 对象，并设置 API 请求的标头

创建一个 `WebClient` 对象，并设置标题。 对必应搜索 API 的所有请求都需要 `Ocp-Apim-Subscription-Key`。 上传图片的 `POST` 请求也必须指定 `ContentType: multipart/form-data`。

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>上传图像，并显示结果

`WebClient` 类的 `UpLoadFile()` 方法为 `POST` 请求设置数据的格式，包括设置 `RequestStream` 的格式和调用 `HttpWebRequest`。

使用 `/details` 终结点和要上传的图像文件调用 `WebClient.UpLoadFile()`。 使用 JSON 响应初始化 `SearchResult` 结构的实例，并存储响应。

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
然后，可将此 JSON 响应打印到控制台。

## <a name="use-an-image-insights-token-in-a-request"></a>在请求中使用图像见解令牌

若要使用由 `POST` 结果返回的 `ImageInsightsToken`，可将其添加到 `GET` 请求。 例如：

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

如果图像中有可识别的人物或地点，则此请求将返回有关它们的信息。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在单页 Web 应用中显示图像和搜索选项](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>另请参阅

* [必应图像搜索 API 参考](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
