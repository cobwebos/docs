---
title: 自定义搜索 SDK C# 快速入门 | Microsoft Docs
titleSuffix: Cognitive Services
description: 设置自定义搜索 SDK C# 控制台应用程序。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365408"
---
# <a name="custom-search-sdk-c-quickstart"></a>自定义搜索 SDK C# 快速入门

必应自定义搜索 SDK 包含用于实体搜索和分析结果的 REST API 功能。

## <a name="application-dependencies"></a>应用程序依赖项

若要使用必应自定义搜索 SDK 设置控制台应用程序，请浏览到 Visual Studio 中的解决方案资源管理器中的“`Manage NuGet Packages`”选项。 添加 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 程序包。

安装 [NuGet 自定义搜索](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)包还会安装依赖项，其中包括以下程序集：
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>实体搜索客户端

若要创建 CustomSearchAPI 客户端的实例，请添加 using 指令：
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

实例化自定义搜索客户端：将 `YOUR-CUSTOM-SEARCH-KEY` 和 `YOUR-CUSTOM-CONFIG-ID` 替换为你的访问密钥以及在[我的实例](https://www.customsearch.ai/)中分配的 API 终结点配置 ID。
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
使用客户端和查询文本进行搜索：
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>分析结果

`SearchAsync` 方法返回 `WebData` 对象，如果针对查询找到任何内容，则包含 `WebPages`。 此代码可找到第一个结果并获取其 `Name` 和 `URL`。
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

 //WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```
## <a name="complete-console-application"></a>完成控制台应用程序

以下代码基于查询“Xbox”进行搜索，并打印出第一个 Web 结果的 `Name` 和 `URL`。
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

            try
            {
                // This will look up a single query (Xbox).
                var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                Console.WriteLine("Searched for Query# \" Xbox \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
                        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find web results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any Web data..");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>后续步骤

[认知服务 .NET SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
