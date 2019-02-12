---
title: 快速入门：通过适用于 C# 的必应实体搜索 SDK 来搜索实体
titleSuffix: Azure Cognitive Services
description: 使用本快速入门通过适用于 C# 的必应实体搜索 SDK 来搜索实体。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: bcb5f77503566b06ee8e3346ec81dfabecbe50a5
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757736"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>通过适用于 C# 的必应实体搜索 SDK 来发送搜索请求

使用本快速入门开始通过适用于 C# 的必应实体搜索 SDK 来搜索实体。 虽然必应实体搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch) 上找到此示例的源代码。


## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [Json.NET](https://www.newtonsoft.com/json) 框架，可以 NuGet 包的形式提供。
* 如果使用的是 Linux/MacOS，则可使用 [Mono](http://www.mono-project.com/) 运行此应用程序。
* [必应新闻搜索 SDK NuGet 程序包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0)。 安装此程序包还会安装以下项：
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

若要向 Visual Studio 项目中添加必应实体搜索 SDK，请使用解决方案资源管理器中的 `Manage NuGet Packages` 选项并添加 `Microsoft.Azure.CognitiveServices.Search.EntitySearch` 包。


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

2. 创建新的搜索客户端。 通过创建新的 `ApiKeyServiceClientCredentials` 添加你的订阅密钥。

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

3. 使用客户端的 `Entities.Search()` 函数来搜索查询：
    
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