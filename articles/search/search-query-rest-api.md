---
title: "查询索引（REST API - Azure 搜索）| Microsoft Docs"
description: "在 Azure 搜索中生成搜索查询，并使用搜索参数对搜索结果进行筛选和排序。"
services: search
documentationcenter: 
manager: jhubbard
author: ashmaka
ms.assetid: 8b3ca890-2f5f-44b6-a140-6cb676fc2c9c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/12/2017
ms.author: ashmaka
ms.openlocfilehash: 49062bec233ad35cd457f9665fa94c1855343582
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="query-your-azure-search-index-using-the-rest-api"></a>使用 REST API 查询 Azure 搜索索引
> [!div class="op_single_selector"]
>
> * [概述](search-query-overview.md)
> * [门户](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

本文介绍如何使用 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)查询索引。

开始本演练前，应已[创建 Azure 搜索索引](search-what-is-an-index.md)并[填充数据](search-what-is-data-import.md)。 有关背景信息，请参阅 [Azure 搜索中全文搜索的工作原理](search-lucene-query-architecture.md)。

## <a name="identify-your-azure-search-services-query-api-key"></a>标识 Azure 搜索服务的查询 API 密钥
为已预配的服务生成的 *API 密钥* 是针对 Azure 搜索 REST API 的每个搜索操作的关键组成部分。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

1. 若要查找服务的 API 密钥，可登录到 [Azure 门户](https://portal.azure.com/)
2. 转到 Azure 搜索服务的边栏选项卡
3. 单击“密钥”图标

服务具有*管理密钥*和*查询密钥*。

* 主管理密钥和辅助 *管理密钥* 授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。 有两个密钥的作用是确保在决定重新生成主密钥时可以继续使用辅助密钥，反之亦然。
* *查询密钥* 授予索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。

可以使用其中一个查询密钥来查询索引。 查询也可使用管理密钥，但最好在应用程序代码中使用查询密钥，因为这更符合 [最低特权原则](https://en.wikipedia.org/wiki/Principle_of_least_privilege)。

## <a name="formulate-your-query"></a>表述查询
有两种方法可以 [使用 REST API 搜索索引](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 一种方法是发出 HTTP POST 请求，这种请求的查询参数在请求主题的 JSON 对象中定义。 另一种方法是发出 HTTP GET 请求，这种请求的查询参数在请求 URL 中定义。 POST 的查询参数大小限制比 GET [宽松](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 因此建议使用 POST，使用 GET 更方便的特殊情况除外。

POST 和 GET 都需要在请求 URL 中提供*服务名称*、*索引名称*和正确的 *API 版本*（发布本文档时的 API 版本为 `2016-09-01`）。 GET 的 URL 末尾为*查询字符串*，用于提供查询参数。 有关 URL 格式，请参见以下内容：

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2016-09-01

POST 的 URL 格式相同，只是查询字符串参数仅包含 API 版本。

#### <a name="example-queries"></a>查询示例
以下是名为“hotels”的索引的几个查询示例。 这些查询以 GET 和 POST 格式显示。

在整个索引中搜索“budget”一词，仅返回 `hotelName` 字段：

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "budget",
    "select": "hotelName"
}
```

对索引应用筛选器，查找价格低于 150 美元/晚的酒店，返回 `hotelId` 和 `description`：

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

搜索整个索引，按特定的字段 (`lastRenovationDate`) 降序排序，接收前两个结果，仅显示 `hotelName` 和 `lastRenovationDate`：

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="submit-your-http-request"></a>提交 HTTP 请求
在 HTTP 请求 URL（针对 GET）或正文（针对 POST）中表述查询后，可定义请求头并提交查询。

#### <a name="request-and-request-headers"></a>请求和请求头
必须为 GET 定义两个请求头，为 POST 定义三个请求头：

1. `api-key` 头必须设置为在上述步骤 I 中找到的查询密钥。 还可以使用管理密钥作为 `api-key` 标头，但建议使用查询密钥，因为它以独占方式对索引和文档授予只读访问权限。
2. `Accept` 头必须设置为 `application/json`。
3. 仅针对 POST，应将 `Content-Type` 头也设置为 `application/json`。

对于 HTTP GET 请求，请参阅以下内容，了解如何使用 Azure 搜索 REST API 搜索“hotels”以及使用简单的查询搜索“motel”一词：

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2016-09-01
Accept: application/json
api-key: [query key]
```

以下是相同的示例查询，只是这次使用 HTTP POST：

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

成功的查询请求会返回状态代码 `200 OK` ，搜索结果则以 JSON 形式在响应正文中返回。 以下是上述查询的结果（假定“hotels”索引填充的是 [使用 REST API 将数据导入 Azure 搜索](search-import-data-rest-api.md) 中的示例数据）（注意，为清楚起见，JSON 已经过格式设置）。

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

要了解详细信息，请参阅 [搜索文档](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)的“响应”部分。 有关请求失败时可能返回的其他 HTTP 状态代码的详细信息，请参阅 [HTTP 状态代码（Azure 搜索）](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)。
