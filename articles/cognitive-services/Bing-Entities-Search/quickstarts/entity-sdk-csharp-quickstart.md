---
title: 快速入门：通过适用于 C# 的 SDK 搜索实体 - 必应实体搜索
titleSuffix: Azure Cognitive Services
description: 使用本快速入门通过适用于 C# 的必应实体搜索 SDK 来搜索实体。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 4c942040a36ae7b103f7dabac62376ea5a4e2890
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384530"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>通过适用于 C# 的必应实体搜索 SDK 来发送搜索请求

使用本快速入门开始通过适用于 C# 的必应实体搜索 SDK 来搜索实体。 虽然必应实体搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch) 上找到此示例的源代码。


## <a name="prerequisites"></a>必备条件

* 任何版本的 [Visual Studio 2017 或更高版本](https://www.visualstudio.com/downloads/)。
* [Json.NET](https://www.newtonsoft.com/json) 框架，可以 NuGet 包的形式提供。
* 如果使用的是 Linux/MacOS，则可使用 [Mono](https://www.mono-project.com/) 运行此应用程序。
* [必应新闻搜索 SDK NuGet 程序包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0)。 安装此程序包还会安装以下项：
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

若要向 Visual Studio 项目中添加必应实体搜索 SDK，请使用**解决方案资源管理器**中的“管理 NuGet 包”  选项并添加 `Microsoft.Azure.CognitiveServices.Search.EntitySearch` 包。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>创建并初始化应用程序

1. 在 Visual Studio 中创建一个新的 C# 控制台解决方案。 然后将以下内容添加到主代码文件。

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>创建客户端并发送搜索请求

1. 创建新的搜索客户端。 通过创建新的 `ApiKeyServiceClientCredentials` 添加你的订阅密钥。

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. 使用客户端的 `Entities.Search()` 函数来搜索查询：
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>获取并输出实体说明

1. 如果 API 返回了搜索结果，则从 `entityData` 获取主实体。

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. 输出主实体的说明 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../tutorial-bing-entities-search-single-page-app.md)

* [什么是必应实体搜索 API？](../overview.md )