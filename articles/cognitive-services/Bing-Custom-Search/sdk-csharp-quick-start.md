---
title: 快速入门：自定义搜索 SDK、C#
titleSuffix: Azure Cognitive Services
description: 设置自定义搜索 SDK C# 控制台应用程序。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 7ac298ad5c5b93b5dce0ce2dd59ffe541888db88
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307757"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>快速入门：通过 C# 使用必应自定义搜索 SDK

必应自定义搜索 SDK 可提供比必应自定义搜索 REST API 更简单的编程模型。 本节将指导你使用 C# SDK 完成第一个自定义搜索调用。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 现成的自定义搜索实例。 请参阅[创建第一个必应自定义搜索实例](quick-start.md)。  
  
- 订阅密钥。 可以在激活[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)时获取订阅密钥，也可以使用 Azure 仪表板中的付费订阅密钥（请参阅[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)）。  另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。
  
- 已安装 visual Studio 2017。 如果没有，可下载免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。  
  
- 已安装 [NuGet 自定义搜索](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)包。 在 Visual Studio 中的解决方案资源管理器中，右键单击项目并从菜单中选择 `Manage NuGet Packages`。 安装 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 包。

安装 NuGet 自定义搜索包还会安装以下程序集：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>运行代码

> [!TIP]
> 从 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch) 获取作为 Visual Studio 解决方案的最新代码。

若要运行该示例，请遵循以下步骤：

1. 打开 Visual Studio 2017。
  
2. 依次单击“文件”菜单、“新建”、“项目”和“Visual C# 控制台应用程序”模板。
  
3. 将解决方案命名为 CustomSearchSolution 并浏览到要将其放入的文件夹。
  
4. 单击“确定”创建解决方案。  
  
4. 在解决方案资源管理器中，右键单击项目（名称与解决方案相同），然后选择 `Manage NuGet Packages`。 单击 NuGet 包管理器中的“浏览”。 在搜索框中输入 Microsoft.Azure.CognitiveServices.Search.CustomSearch 并按 Enter。 选择包，然后单击“安装”。  
  
4. 将以下代码复制到 Program.cs 文件。 将 YOUR-SUBSCRIPTION-KEY 和 YOUR-CUSTOM-CONFIG-ID 替换为订阅密钥和配置 ID。  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

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
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
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

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. 生成并运行（调试）解决方案。 




## <a name="breaking-it-down"></a>细分

安装 NuGet 自定义搜索包之后，需要为其添加一条 using 指令。

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

接下来，实例化自定义搜索客户端，可用于提出搜索请求。 确保将 `YOUR-SUBSCRIPTION-KEY` 替换为你的密钥。

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

现在，可以使用客户端发送搜索请求。 请务必将 `YOUR-CUSTOM-CONFIG-ID` 替换为实例的配置 ID（可在[自定义搜索门户](https://www.customsearch.ai/)中找到 ID）。 此示例将搜索 Xbox。 根据需要进行更新。

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

`SearchAsync` 方法将返回 `WebData` 对象。 使用 `WebData` 循环访问找到的任何 `WebPages`。 此代码可查找第一个网页结果并打印网页的 `Name` 和 `URL`。

```csharp
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


## <a name="next-steps"></a>后续步骤

查看 SDK 示例。 每个示例都包括一个自述文件，其中包含有关先决条件和安装/运行示例的详细信息。

* 开始使用 [.NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * 有关定义和依赖项，另请参阅 [.NET 库](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch)。
* 开始使用 [NodeJS 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [NodeJS 库](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch)。
* 开始使用 [Java 示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Java 库](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch)。
* 开始使用 [Python 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * 有关定义和依赖项，另请参阅 [Python 库](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch)。

