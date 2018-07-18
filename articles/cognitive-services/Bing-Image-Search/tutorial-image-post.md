---
title: 上传必应图像以获取见解 | Microsoft Docs
description: 使用必应图像搜索 API 上传图像并获取图像见解的控制台应用程序。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365420"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>教程：上传必应图像以获取见解

必应图像搜索 API 提供 `POST` 选项来上传图像并搜索与图像相关的信息。 此 C# 控制台应用程序使用图像搜索终结点上传图像以获取有关图像的详细信息。
简而言之，结果是 JSON 对象，如下所示：

![[JSON 结果]](media/cognitive-services-bing-images-api/jsonResult.jpg)

本教程介绍了如何完成以下操作：

> [!div class="checklist"]
> * 在 `POST` 请求中使用图像搜索 `/details` 终结点
> * 指定请求标头
> * 使用 URL 参数指定结果
> * 上传图像数据并发送 `POST` 请求
> * 将 JSON 结果传输到控制台

## <a name="app-components"></a>应用组件

教程应用程序包括三个部分：

> [!div class="checklist"]
> * 终结点配置，用于指定必应图像搜索终结点和所需标头
> * 将 `POST` 请求的图像文件上传到终结点
> * 解析 JSON 结果，这些结果是从 `POST` 请求返回的详细信息

## <a name="scenario-overview"></a>方案概述
存在[三种图像搜索终结点](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint)。 `/details` 终结点可在请求正文中使用带有图像数据的 `POST` 请求。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
`/details?` 后面的 `modules` URL 参数指定结果包含的详细信息类型：
* `modules=All`
* `modules=RecognizedEntities`（图像中可见的人物或地点）

在 `POST` 请求中指定 `modules=All` 以获取包含以下列表的 JSON 文本：
* `bestRepresentativeQuery` - 返回与上传图像类似图像的必应查询
* `detectedObjects`，如图像中的边框或热点
* `image` 元数据
* `imageInsightsToken` - 获取 `RecognizedEntities` 的后续 `GET` 请求的令牌（图片中可见的人或地点） 
* `imageTags`
* `pagesIncluding` - 包括该图像的网页
* `relatedSearches`
* `visuallySimilarImages`

在 `POST` 请求中指定 `modules=RecognizedEntities`，以仅获取后续 `GET` 请求中使用的 `imageInsightsToken`。 它标识图像中可见的人物或地点。

## <a name="webclient-and-headers-for-the-post-request"></a>POST 请求的 WebClient 和标头
创建一个 `WebClient` 对象，并设置标题。 对必应搜索 API 的所有请求都需要 `Ocp-Apim-Subscription-Key`。 上传图片的 `POST` 请求也必须指定 `ContentType: multipart/form-data`。

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>上传图像并获得结果

`WebClient` 类包含格式化 `POST` 请求数据的 `UpLoadFile` 方法。 它格式化 `RequestStream` 并调用 `HttpWebRequest`，大大降低了复杂性。
使用 `/details` 终结点和要上传的图像文件调用 `WebClient.UpLoadFile`。 响应是二进制数据，可以轻松转换为 JSON 格式。 

使用 JSON 文本初始化 `SearchResult` 结构的实例（有关上下文，请参阅[应用程序源代码](tutorial-image-post-source.md)）。
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>传输结果
其余代码解析 JSON 结果并将其传输到控制台。

```
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
```
## <a name="get-request-using-the-imageinsightstoken"></a>使用 ImageInsightsToken 的 GET 请求
要使用返回结果为 `POST` 的 `ImageInsightsToken`，请创建如下所示的 `GET` 请求：
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
如果图像中有可识别的人物或地点，则此请求将返回有关它们的信息。
[快速入门](https://docs.microsoft.com/azure/cognitive-services/bing-image-search)包含许多代码示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

