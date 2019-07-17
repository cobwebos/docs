---
title: 快速入门：使用 Postman 和 REST API 创建、加载和查询索引 - Azure 搜索
description: 了解如何使用 Postman 调用 Azure 搜索 REST API 并了解示例数据和定义。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ee3c4da96629910801413cbbad69963defb87dfe
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798780"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>快速入门：使用 REST API 在 Postman 中创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [门户](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

若要探索 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice)，最轻松的方式之一是使用 Postman 或其他 Web 测试工具来构建 HTTP 请求并检查响应。 在编写任何代码之前，可以使用适当的工具按照这些说明发送请求和查看响应。

如果还没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，然后[注册 Azure 搜索](search-create-service-portal.md)。

## <a name="prerequisites"></a>先决条件

本快速入门使用以下服务和工具。 

+ [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

+ 使用 [Postman 桌面应用](https://www.getpostman.com/)将请求发送到 Azure 搜索。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。  示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="connect-to-azure-search"></a>连接到 Azure 搜索

在本部分，我们将使用所选的 Web 工具来与 Azure 搜索建立连接。 每个工具都会持久保留会话的请求标头信息，这意味着，只需输入 api-key 和 Content-Type 一次即可。

使用任一工具都需要选择一个命令（GET、POST、PUT 等）并提供 URL 终结点；对于某些任务，需要在请求正文中提供 JSON。 将搜索服务名称 (YOUR-SEARCH-SERVICE-NAME) 替换为一个有效值。 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

请注意 HTTPS 前缀、服务的名称、对象（在本例中为索引集合）的名称和 [api-version](search-api-versions.md)。 api-version 是必需的小写字符串；对于当前版本，它指定为 `?api-version=2019-05-06`。 API 版本定期更新。 将 api-version 包括在每个请求中即可完全控制要使用的版本。  

请求标头组合包括两个元素：内容类型，以及用于在 Azure 搜索中进行身份验证的 api-key。 将管理员 API 密钥 (YOUR-ADMIN-API-KEY) 替换为一个有效值。 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

在 Postman 中，构建如以下屏幕截图所示的请求。 选择“GET”作为谓词，提供 URL，然后单击“发送”。   此命令连接到 Azure 搜索，读取索引集合，并在成功连接后返回 HTTP 状态代码 200。 如果服务已有索引，则响应还会包含索引定义。

![Postman 请求标头][6]

## <a name="1---create-an-index"></a>1 - 创建索引

在 Azure 搜索中，通常会先创建索引，然后再连同数据一起加载索引。 本任务将使用[创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)。 

需扩展 URL 以包含 `hotels` 索引名称。

为此，请在 Postman 中：

1. 将谓词更改为“PUT”。 

2. 复制此 URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`。

3. 在请求正文中提供索引定义（如下所示）。

4. 单击“Send”  。

![Postman 请求正文][8]

### <a name="index-definition"></a>索引定义

字段集合定义文档结构。 每个文档必须包含这些字段，每个字段必须具有一个数据类型。 字符串字段用于全文搜索，因此，如果要求数值数据可供搜索，则需将该内容强制转换为字符串。

字段的属性决定了允许的操作。 默认情况下，REST API 允许很多操作。 例如，默认情况下，所有字符串均可供搜索、检索、筛选、分面。 通常，仅当需要禁用某种行为时，才要设置属性。

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

提交此请求后，会获得 HTTP 201 响应，指示索引已成功创建。 可以在门户中验证此操作，但请注意，门户页有刷新时间间隔，因此可能需要等待一到两分钟。

> [!TIP]
> 如果收到 HTTP 504，请验证该 URL 是否指定了 HTTPS。 如果看到 HTTP 400 或 404，请检查请求正文，以验证是否没有复制粘贴错误。 HTTP 403 通常指示 api-key 有问题（密钥无效或指定 api-key 的方式有语法问题）。

## <a name="2---load-documents"></a>2 - 加载文档

创建索引和填充索引是分开的步骤。 在 Azure 搜索中，索引包含所有可搜索数据，这些数据可以作为 JSON 文档提供。 本任务将使用[添加、更新或删除文档 REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。 

需扩展 URL 以包含 `docs` 集合与 `index` 操作。

为此，请在 Postman 中：

1. 将谓词更改为  POST。

2. 复制此 URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`。

3. 在请求正文中提供 JSON 文档（如下所示）。

4. 单击“Send”  。

![Postman 请求有效负载][10]

### <a name="json-documents-to-load-into-the-index"></a>要载入索引的 JSON 文档

请求正文包含四个要添加到 hotels 索引的文档。

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "description_fr": "Hôtel le moins cher en ville",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

在几秒钟内，应在会话列表中看到 HTTP 200 响应。 这指示已成功创建文档。 

如果收到 207，则指示至少有一个文档无法上传。 如果收到 404，则表示请求的标头或正文有语法错误：请验证是否已更改终结点，使之包括 `/docs/index`。

> [!Tip]
> 对于所选数据源，可以选择备用的  indexer 方法，以便简化并减少进行索引操作所需的代码量。 有关详细信息，请参阅 [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)（Indexer 操作）。


## <a name="3---search-an-index"></a>3 - 搜索索引

现在，索引和文档已加载，可以使用[搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 针对它们发出查询了。

需扩展 URL，以包含使用搜索运算符指定的查询字符串。

为此，请在 Postman 中：

1. 将谓词更改为 **GET**。

2. 复制此 URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`。

3. 单击“Send”  。

此查询针对“motel”一词进行搜索，在搜索结果中返回文档的计数。 在单击“发送”后，请求和响应应类似于以下针对 Postman 的屏幕截图  。 状态代码应为 200。

 ![Postman 查询响应][11]

## <a name="get-index-properties"></a>获取索引属性
还可以查询系统信息，获取文档计数和存储使用量：`https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

在 Postman 中，请求应如下所示，响应包括文档计数和所用空间（以字节为单位）。

 ![Postman 系统查询][12]

请注意，api-version 语法有所不同。 对于此请求，请使用 `?` 来追加 api-version。 `?` 将 URL 路径与查询字符串分隔开，而 & 将查询字符串中的每个“名称=值”对分隔开。 就此查询来说，api-version 是查询字符串中的第一个项，也是唯一项。

有关此 API 的详细信息，请参阅[获取索引统计信息 REST API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics)。

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组，以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接   ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

REST 客户端对于即席探索很有用，但在了解 REST API 工作原理以后，则可继续代码操作。 有关后续步骤，请参阅以下链接：

+ [快速入门：使用 .NET SDK 创建索引](search-create-index-dotnet.md)
+ [快速入门：使用 PowerShell 创建索引 (REST)](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-get-started-postman/fiddler-url.png
[2]: ./media/search-get-started-postman/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-get-started-postman/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-get-started-postman/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-get-started-postman/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-get-started-postman/postman-url.png
[7]: ./media/search-get-started-postman/fiddler-request.png
[8]: ./media/search-get-started-postman/postman-request.png
[9]: ./media/search-get-started-postman/fiddler-docs.png
[10]: ./media/search-get-started-postman/postman-docs.png
[11]: ./media/search-get-started-postman/postman-query.png
[12]: ./media/search-get-started-postman/postman-system-query.png
