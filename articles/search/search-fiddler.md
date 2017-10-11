---
title: "如何使用 Fiddler 评估和测试 Azure 搜索 REST API| Microsoft Docs"
description: "使用 Fiddler 实现验证 Azure 搜索可用性和试用 REST API 的无代码方法。"
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: 790e5779-c6a3-4a07-9d1e-d6739e6b87d2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: heidist
ms.openlocfilehash: c38b73fa69bee34ce2434c6274cb017c99ef3c35
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>使用 Fiddler 评估和测试 Azure 搜索 REST API
> [!div class="op_single_selector"]
>
> * [概述](search-query-overview.md)
> * [搜索浏览器](search-explorer.md)
> * [Fiddler](search-fiddler.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

本文介绍如何使用 Fiddler（以 [Telerik 的免费下载项](http://www.telerik.com/fiddler)的形式提供）通过 Azure 搜索 REST API（而无需编写任何代码）发出 HTTP 请求并查看响应。 Azure 搜索是在 Microsoft Azure 上完全托管的托管云搜索服务，可通过 .NET 和 REST API 简化编程。 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)上介绍了 Azure 搜索服务 REST API。

在以下步骤中，将创建索引，上传文档，查询索引，然后在系统中查询服务信息。

若要完成这些步骤，需要 Azure 搜索服务和 `api-key`。 有关如何开始使用的说明，请参阅 [在门户中创建 Azure 搜索服务](search-create-service-portal.md) 。

## <a name="create-an-index"></a>创建索引
1. 启动 Fiddler。 在“文件”菜单上，关闭“捕获流量”，隐藏与当前任务无关的外部 HTTP 活动。
2. 在“编辑器”选项卡上，将构建如以下屏幕截图所示的请求。

      ![][1]
3. 选择“PUT” 。
4. 输入一个 URL，其中指定服务 URL、请求属性和 api 版本。 要记住的几个要点：

   * 使用 HTTPS 作为前缀。
   * 请求属性是“/indexes/hotels”。 这会指示搜索创建名为“hotels”的索引。
   * Api 版本采用小写，指定为“?api-version=2016-09-01”。 API 版本很重要，因为 Azure 搜索定期部署更新。 在极少数情况下，服务更新可能会对 API 引入重大更改。 由于此原因，Azure 搜索要求在每个请求中都指定 api 版本，这样用户可以完全控制使用哪一个版本。

     完整的 URL 应如以下示例所示。

             https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01
5. 指定请求标头，并将 host 和 api-key 替换为对服务有效的值。

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
6. 在请求正文中，粘贴构成索引定义的字段。

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
7. 单击“执行” 。

在几秒钟内，应在会话列表中看到 HTTP 201 响应，该值指示索引已成功创建。

如果收到 HTTP 504，请验证该 URL 是否指定了 HTTPS。 如果看到 HTTP 400 或 404，请检查请求正文，以验证是否没有复制粘贴错误。 HTTP 403 通常指示 api-key 有问题（密钥无效或指定 api-key 的方式有语法问题）。

## <a name="load-documents"></a>加载文档
在“编辑器”选项卡上，发布文档的请求将如下所示。 请求正文包含搜索 4 个酒店的数据。

   ![][2]

1. 选择“POST” 。
2. 输入以 HTTPS 开头，后接服务 URL，再后接“/indexes/<'indexname'>/docs/index?api-version=2016-09-01”的 URL。 完整的 URL 应如以下示例所示。

         https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
3. 请求标头应与前面的相同。 请记住，将 host 和 api-key 替换为对服务有效的值。

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
4. 请求正文包含四个要添加到 hotels 索引的文档。

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
5. 单击“执行” 。

在几秒钟内，应在会话列表中看到 HTTP 200 响应。 这指示已成功创建文档。 如果收到 207，则指示至少有一个文档无法上传。 如果收到 404，则指示请求的标头或正文中有语法错误。

## <a name="query-the-index"></a>查询索引
现在，已加载索引和文档，可以针对它们发出查询了。  在“编辑器”选项卡上，用于查询服务的 **GET** 命令将如下面的屏幕截图所示。

   ![][3]

1. 选择“GET” 。
2. 输入以 HTTPS 开头，后跟服务 URL，后跟“/indexes/<'indexname'>/docs?”，后跟查询参数的 URL。 例如，使用以下 URL，并将示例主机名替换为对服务有效的主机名。

         https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2016-09-01

   此查询将搜索词语“motel”并检索分级的 facet 类别。
3. 请求标头应与前面的相同。 请记住，将 host 和 api-key 替换为对服务有效的值。

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444

响应代码应是 200，并且响应输出应如下面的屏幕截图所示。

   ![][4]

以下示例查询取自 MSDN 上的 [搜索索引操作（Azure 搜索 API）](http://msdn.microsoft.com/library/dn798927.aspx) 。 本主题中的许多示例查询包含空格，这在 Fiddler 中是不允许的。 在粘贴查询字符串前，请将每个空格替换为“+”字符，然后再在 Fiddler 中尝试查询。

**替换空格前：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**使用“+”替换空格后：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-the-system"></a>查询系统
还可以查询系统以获取文档计数和存储使用量。 在“编辑器”选项卡上，请求将如下所示，并且响应将返回文档计数和已用空间。

 ![][5]

1. 选择“GET” 。
2. 输入 URL，其中包含服务 URL，后接“/indexes/hotels/stats?api-version=2016-09-01”：

         https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01
3. 指定请求标头，并将 host 和 api-key 替换为对服务有效的值。

         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
4. 将请求正文留空。
5. 单击“执行” 。 应在会话列表中看到 HTTP 200 状态代码。 选择为命令发布的条目。
6. 单击“检查器”选项卡，单击“标头”选项卡，并选择 JSON 格式。 应看到文档计数和存储大小（以 KB 为单位）。

## <a name="next-steps"></a>后续步骤
有关使用无代码方法管理和使用 Azure 搜索，请参阅 [在 Azure 上管理搜索服务](search-manage.md) 。

<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
