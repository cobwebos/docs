---
title: 在 Fiddler 或 Postman（Azure 搜索 REST）中探索 REST API | Microsoft Docs
description: 如何使用 Fiddler 或 Postman 发出对 Azure 搜索的 HTTP 请求和 REST API 调用。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 01/04/2018
ms.author: heidist
ms.openlocfilehash: 6108e0061c4a8de3000de7f7a07cca313803e80d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>使用 Fiddler 或 Postman 探索 Azure 搜索 REST API

若要探索 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice)，最轻松的方式之一是使用 Fiddler 或 Postman 来构建 HTTP 请求并检查响应。 在编写任何代码之前，可以使用适当的工具按照这些说明发送请求和查看响应。

> [!div class="checklist"]
> * 下载 Web API 测试工具
> * 获取搜索服务的 api-key 和终结点
> * 配置请求标头
> * 创建索引
> * 加载索引
> * 搜索索引

如果还没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，然后[注册 Azure 搜索](search-create-service-portal.md)。

## <a name="download-and-install-tools"></a>下载并安装工具

以下工具广泛用于 Web 开发，但如果你熟悉其他工具，则仍可在使用该工具时遵循本文中的说明。

+ [Postman 桌面应用](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>获取 api-key 和终结点

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 搜索，则请按以下步骤获取必需信息：

1. 在 Azure 门户中，从仪表板打开搜索服务页，或者在服务列表中[查找服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
2. 在“概览” > “重要信息” > “URL”中获取终结点。 示例终结点可能类似于 `https://my-service-name.search.windows.net`。
3. 在“设置” > “密钥”中获取 api-key。 有两个冗余性的管理密钥，在需要滚动更新密钥时可以使用。 管理密钥授予对服务的写入权限，这是创建和加载索引所必需的。 可以使用主密钥或辅助密钥进行写入操作。

## <a name="configure-request-headers"></a>配置请求标头

每个工具都会持久保留会话的请求标头信息，这意味着只需输入 URL 终结点、api-version、api-key 和 content-type 一次。

完整的 URL 应类似于以下示例，唯一区别是，应对 **`my-app`** 占位符名称 `https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01` 进行有效的替换。

服务 URL 组合包括以下元素：

+ HTTPS 前缀。
+ 从门户获取的服务 URL。
+ 资源：一项操作，用于创建基于服务的对象。 在此步骤中，它是名为“hotels”的索引。
+ api-version，一个必需的小写字符串，已针对当前版本指定为“?api-version=2016-09-01”。 [API 版本](search-api-versions.md)定期更新。 将 api-version 包括在每个请求中即可完全控制要使用的版本。  

请求标头组合包括 content-type 和 api-key 这两个元素，如前一部分所述：

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

构建一个如以下屏幕截图所示的请求。 选择 PUT 作为谓词。 Fiddler 添加 `User-Agent=Fiddler`。 可以在其下的新行中粘贴这两个额外的请求标头。 使用服务的管理员访问密钥，包括适用于服务的 content-type 和 api-key。

![Fiddler 请求标头][1]

> [!Tip]
> 可以关闭 Web 流量，以便隐藏与所执行任务无关的 HTTP 活动。 在 Fiddler 中转到“文件”菜单，关闭“捕获流量”。 

### <a name="postman"></a>Postman

构建一个如以下屏幕截图所示的请求。 选择 PUT 作为谓词。 

![Postman 请求标头][6]

## <a name="create-the-index"></a>创建索引

请求正文包含索引定义。 添加请求正文以后，即完成了用于生成索引的请求。

除了索引名称，请求中最重要的组件是字段集合。 字段集合定义索引架构。 在每个字段中指定其类型。 字符串字段用于全文搜索，因此，如果要求数值数据可供搜索，则需将该内容强制转换为字符串。

字段的属性决定了允许的操作。 默认情况下，REST API 允许很多操作。 例如，默认情况下，所有字符串均可供搜索、检索、筛选、分面。 通常情况下，只有在需要关闭某个行为时，才需设置属性。 有关属性的详细信息，请参阅 [Create Index (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index)（创建索引 (REST)）。

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
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

如果收到 HTTP 504，请验证该 URL 是否指定了 HTTPS。 如果看到 HTTP 400 或 404，请检查请求正文，以验证是否没有复制粘贴错误。 HTTP 403 通常指示 api-key 有问题（密钥无效或指定 api-key 的方式有语法问题）。

### <a name="fiddler"></a>Fiddler

将索引定义复制到请求正文（类似于以下屏幕截图），然后单击右上角的“执行”，以便发送完成的请求。

![Fiddler 请求正文][7]

### <a name="postman"></a>Postman

将索引定义复制到请求正文（类似于以下屏幕截图），然后单击右上角的“发送”，以便发送完成的请求。

![Postman 请求正文][8]

## <a name="load-documents"></a>加载文档

创建索引和填充索引是分开的步骤。 在 Azure 搜索中，索引包含所有可搜索数据，这些数据可以作为 JSON 文档提供。 若要查看此操作的 API，请参阅 [Add, update, or delete documents (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)（添加、更新或删除文档 (REST)）。

+ 将谓词更改为 POST（适用于此步骤）。
+ 更改终结点，使之包括 `/docs/index`。 完整的 URL 应类似于 `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01`
+ 按原样保留请求标头。 

请求正文包含四个要添加到 hotels 索引的文档。

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
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
> 对于所选数据源，可以选择备用的 indexer 方法，以便简化并减少进行索引操作所需的代码量。 有关详细信息，请参阅 [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)（Indexer 操作）。

### <a name="fiddler"></a>Fiddler

将谓词更改为 POST。 更改 URL，使之包括 `/docs/index`。 将文档复制到请求正文中（类似于以下屏幕截图），然后执行请求。

![Fiddler 请求有效负载][9]

### <a name="postman"></a>Postman

将谓词更改为 POST。 更改 URL，使之包括 `/docs/index`。 将文档复制到请求正文中（类似于以下屏幕截图），然后执行请求。

![Postman 请求有效负载][10]

## <a name="query-the-index"></a>查询索引
现在，已加载索引和文档，可以针对它们发出查询了。 有关此 API 的详细信息，请参阅 [Search Documents (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents)（搜索文档 (REST)）  

+ 将谓词更改为 GET（适用于此步骤）。
+ 更改终结点，使之包括查询参数（包括搜索字符串）。 查询 URL 可能类似于 `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2016-09-01`
+ 按原样保留请求标头

此查询针对“motel”一词进行搜索，在搜索结果中返回文档的计数。 在单击“发送”后，请求和响应应该类似于以下针对 Postman 的屏幕截图。 状态代码应为 200。

 ![Postman 查询响应][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>在 Fiddler 中运行示例查询的提示

以下示例查询取自 [Search Index operation (Azure Search API)](http://msdn.microsoft.com/library/dn798927.aspx)（搜索索引操作（Azure 搜索 API））一文。 本问中的许多示例查询包含空格，这在 Fiddler 中是不允许的。 在粘贴查询字符串前，请将每个空格替换为“+”字符，然后再在 Fiddler 中尝试查询。

替换空格之前 (lastRenovationDate desc)：

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

将空格替换为 + 之后 (lastRenovationDate+desc)：

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-index-properties"></a>查询索引属性
还可以查询系统信息，获取文档计数和存储使用量：`https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01`

在 Postman 中，请求应如下所示，响应包括文档计数和所用空间（以字节为单位）。

 ![Postman 系统查询][12]

请注意，api-version 语法有所不同。 对于此请求，请使用 `?` 来追加 api-version。 ? 将 URL 路径与查询字符串分开，而 & 则用于分隔查询字符串中的每个“名称=值”对。 就此查询来说，api-version 是查询字符串中的第一个项，也是唯一项。

有关此 API 的详细信息，请参阅 [Get Index Statistics (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics)（获取索引统计信息 (REST)）。


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>在 Fiddler 中查看索引统计信息的提示

在 Fiddler 中单击“检查器”选项卡，再单击“标头”选项卡，然后选择 JSON 格式。 应看到文档计数和存储大小（以 KB 为单位）。

## <a name="next-steps"></a>后续步骤

REST 客户端对于即席探索很有用，但在了解 REST API 工作原理以后，则可继续代码操作。 有关后续步骤，请参阅以下链接：

+ [创建索引 (REST)](search-create-index-rest-api.md)
+ [导入数据 (REST)](search-import-data-rest-api.md)
+ [搜索索引 (REST)](search-query-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png