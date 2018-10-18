---
title: 快速入门：使用 C# 调用终结点 - 必应自定义搜索
titlesuffix: Azure Cognitive Services
description: 本快速入门演示如何通过使用 C# 调用必应自定义搜索终结点来从自定义搜索实例中请求搜索结果。
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 1c3b1031c2d08b1f346216b54d351c99f01db933
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167301"
---
# <a name="quickstart-call-bing-custom-search-endpoint-c"></a>快速入门：调用必应自定义搜索终结点 (C#)

本快速入门演示如何使用 C# 调用必应自定义搜索终结点来从自定义搜索实例中请求搜索结果。 

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 现成的自定义搜索实例。 请参阅[创建第一个必应自定义搜索实例](quick-start.md)。
- 已安装 [.Net Core](https://www.microsoft.com/net/download/core)。
- 订阅密钥。 可以在激活[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)时获取订阅密钥，也可以使用 Azure 仪表板中的付费订阅密钥（请参阅[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)）。    


## <a name="run-the-code"></a>运行代码

若要运行该示例，请遵循以下步骤：

1. 为代码创建文件夹。  
  
2. 从命令提示符或终端导航至刚刚创建的文件夹。  
  
3. 运行以下命令：
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. 将以下代码复制到 Program.cs。 将“YOUR-SUBSCRIPTION-KEY”和“YOUR-CUSTOM-CONFIG-ID”分别替换为订阅密钥和配置 ID。

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. 使用以下命令来生成应用程序。 请注意由命令输出引用的 DLL 路径。

    <pre>
    dotnet build 
    </pre>
    
7. 使用下面的命令来运行应用程序，将 PATH TO OUTPUT 替换为步骤 6 中引用的 DLL 路径。

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>后续步骤
- [配置托管 UI 体验](./hosted-ui.md)
- [使用修饰标记来突出显示文本](./hit-highlighting.md)
- [网页](./page-webpages.md)
