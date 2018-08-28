---
title: 快速入门：使用 C# 向必应图像搜索 API 发送搜索查询（使用 REST API）
description: 本快速入门将使用 C# 向必应搜索 API 发送搜索查询，以获取相关图像的列表。
services: cognitive-services
documentationcenter: ''
author: aahill
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/9/2018
ms.author: aahi
ms.openlocfilehash: 7a5ef36f02d82ee17698af9c647f043792280fbc
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929909"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-c"></a>快速入门：使用 REST API 和 C# 发送搜索查询

必应图像搜索 API 通过允许向必应发送用户搜索查询并获取相关图像的列表，提供与 Bing.com/图像相似的体验。

本文包括一个简单的控制台应用程序，它执行必应图像搜索 API 查询并显示返回的原始搜索结果，这些结果采用 JSON 格式。 虽然此应用程序是用 C# 编写的，但 API 是一种 RESTful Web 服务，与任何可以发出 HTTP 请求并分析 JSON 的编程语言兼容。 

示例程序仅使用了 .NET Core 类，并使用 .NET CLR 在 Windows 上运行，或者使用 [Mono](http://www.mono-project.com/) 在 Linux 或 macOS 上运行。

## <a name="prerequisites"></a>先决条件

需要使用 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 才能在 Windows 上运行此代码。 （免费的社区版也可以。）

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-application"></a>运行应用程序

要运行此应用程序，请执行以下步骤。

1. 在 Visual Studio 中创建一个新的控制台解决方案。
2. 将 `Program.cs` 替换为所提供的代码。
3. 使用对订阅有效的访问密钥替换 `accessKey` 值。
4. 运行该程序。

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;

namespace BingSearchApisQuickstart
{

    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        // Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
        // search APIs.  In the future, regional endpoints may be available.  If you
        // encounter unexpected authorization errors, double-check this value against
        // the endpoint for your Bing search instance in your Azure dashboard.
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

        const string searchTerm = "puppies";

        // Used to return image search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;

            if (accessKey.Length == 32)
            {
                Console.WriteLine("Searching images for: " + searchTerm);

                SearchResult result = BingImageSearch(searchTerm);

                Console.WriteLine("\nRelevant HTTP Headers:\n");
                foreach (var header in result.relevantHeaders)
                    Console.WriteLine(header.Key + ": " + header.Value);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(result.jsonResult));
            }
            else
            {
                Console.WriteLine("Invalid Bing Search API subscription key!");
                Console.WriteLine("Please paste yours into the source code.");
            }

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Image search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingImageSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult()
            {
                jsonResult = json,
                relevantHeaders = new Dictionary<String, String>()
            };

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
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

## <a name="json-response"></a>JSON 响应

示例响应如下。 为了限制 JSON 的长度，系统仅显示一个结果，并截断了响应的其他部分。 

```json
{
  "_type": "Images",
  "instrumentation": {},
  "readLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=puppies",
  "webSearchUrl": "https://www.bing.com/images/search?q=puppies&FORM=OIIARP",
  "totalEstimatedMatches": 955,
  "nextOffset": 1,
  "value": [
    {
      "webSearchUrl": "https://www.bing.com/images/search?view=detailv...",
      "name": "So cute - Puppies Wallpaper",
      "thumbnailUrl": "https://tse3.mm.bing.net/th?id=OIP.jHrihoDNkXGS1t...",
      "datePublished": "2014-02-01T21:55:00.0000000Z",
      "contentUrl": "http://images4.contoso.com/image/photos/14700000/So-cute-puppies...",
      "hostPageUrl": "http://www.contoso.com/clubs/puppies/images/14749028/...",
      "contentSize": "394455 B",
      "encodingFormat": "jpeg",
      "hostPageDisplayUrl": "www.contoso.com/clubs/puppies/images/14749...",
      "width": 1600,
      "height": 1200,
      "thumbnail": {
        "width": 300,
        "height": 225
      },
      "imageInsightsToken": "ccid_jHrihoDN*mid_F68CC526226E163FD1EA659747AD...",
      "insightsMetadata": {
        "recipeSourcesCount": 0
      },
      "imageId": "F68CC526226E163FD1EA659747ADCB8F9FA36",
      "accentColor": "8D613E"
    }
  ],
  "queryExpansions": [
    {
      "text": "Shih Tzu Puppies",
      "displayText": "Shih Tzu",
      "webSearchUrl": "https://www.bing.com/images/search?q=Shih+Tzu+Puppies...",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Shih...",
      "thumbnail": {
        "thumbnailUrl": "https://tse2.mm.bing.net/th?q=Shih+Tzu+Puppies&pid=Api..."
      }
    }
  ],
  "pivotSuggestions": [
    {
      "pivot": "puppies",
      "suggestions": [
        {
          "text": "Dog",
          "displayText": "Dog",
          "webSearchUrl": "https://www.bing.com/images/search?q=Dog&tq=%7b%22pq%...",
          "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Dog...",
          "thumbnail": {
            "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Dog&pid=Api&mkt=en-US..."
          }
        }
      ]
    }
  ],
  "similarTerms": [
    {
      "text": "cute",
      "displayText": "cute",
      "webSearchUrl": "https://www.bing.com/images/search?q=cute&FORM=...",
      "thumbnail": {
        "url": "https://tse2.mm.bing.net/th?q=cute&pid=Api&mkt=en-US..."
      }
    }
  ],
  "relatedSearches": [
    {
      "text": "Cute Puppies",
      "displayText": "Cute Puppies",
      "webSearchUrl": "https://www.bing.com/images/search?q=Cute+Puppies",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/sear...",
      "thumbnail": {
        "thumbnailUrl": "https://tse4.mm.bing.net/th?q=Cute+Puppies&pid=..."
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>另请参阅 

[必应图像搜索概述](../overview.md)  
[试用](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[获取免费试用访问密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[必应图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
