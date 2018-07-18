---
title: 上传数据（REST API - Azure 搜索）| Microsoft Docs
description: 了解如何使用 REST API 将数据上传到 Azure 搜索索引。
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.openlocfilehash: 53b20c9db7efe1f8876eec7c0167dc151aa38786
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187974"
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>使用 REST API 将数据上传到 Azure 搜索
> [!div class="op_single_selector"]
>
> * [概述](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

本文介绍如何使用 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/) 将数据导入 Azure 搜索索引。

开始本演练前，应已 [创建 Azure 搜索索引](search-what-is-an-index.md)。

为了使用 REST API 将文档推送到索引中，需要对索引的 URL 终结点发出 HTTP POST 请求。 HTTP 请求的正文是一个 JSON 对象，包含要添加、修改或删除的文档。

## <a name="identify-your-azure-search-services-admin-api-key"></a>确定 Azure 搜索服务的管理 API 密钥
使用 REST API 对服务发出 HTTP 请求时， *每个* API 请求必须包含为已预配的搜索服务生成的 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

1. 若要查找服务的 API 密钥，可登录到 [Azure 门户](https://portal.azure.com/)
2. 转到 Azure 搜索服务的边栏选项卡
3. 单击“密钥”图标

服务将具有*管理密钥*和*查询密钥*。

* 主管理密钥和辅助 *管理密钥* 授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。 有两个密钥的作用是确保在决定重新生成主密钥时可以继续使用辅助密钥，反之亦然。
* *查询密钥* 授予索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。

可以使用主管理密钥或辅助管理密钥将数据导入到索引中。

## <a name="decide-which-indexing-action-to-use"></a>确定要使用的索引操作
使用 REST API 时，将向 Azure 搜索索引的终结点 URL 发出具有 JSON 请求正文的 HTTP POST 请求。 HTTP 请求正文中的 JSON 对象包含一个名为“value”的 JSON 数组，该数组中包含一系列 JSON 对象，表示要添加到索引、更新或删除的文档。

“value”数组中的每个 JSON 对象表示要索引的文档。 每个对象都包含文档密钥，并指定所需的索引操作（upload、merge、delete 等）。 根据选择的以下操作，每个文档必须仅包含某些特定的字段：

| @search.action | 说明 | 每个文档必需的字段 | 说明 |
| --- | --- | --- | --- |
| `upload` |`upload` 操作类似于“upsert”，如果文档是新文档，则插入；如果文档已经存在，则进行更新/替换。 |关键字段以及要定义的任何其他字段 |更新/替换现有文档时，会将请求中未指定的任何字段设置为 `null`。 即使该字段之前设置为了非 null 值也是如此。 |
| `merge` |使用指定的字段更新现有文档。 如果索引中不存在该文档，merge 会失败。 |关键字段以及要定义的任何其他字段 |merge 中指定的任何字段都将替换文档中的现有字段。 包括 `Collection(Edm.String)`类型的字段。 例如，如果文档包含值为 `["budget"]` 的字段 `tags`，并且已使用值 `["economy", "pool"]` 对 `tags` 执行合并，则 `tags` 字段的最终值将为 `["economy", "pool"]`。 而不会是 `["budget", "economy", "pool"]`。 |
| `mergeOrUpload` |如果索引中已存在具有给定关键字段的文档，则此操作的行为类似于 `merge`。 如果该文档不存在，则它的行为类似于对新文档进行 `upload` 。 |关键字段以及要定义的任何其他字段 |- |
| `delete` |从索引中删除指定文档。 |仅关键字段 |所指定关键字段以外的所有字段都会被忽略。 如果要从文档中删除单个字段，请改用 `merge`，只需将该字段显式设置为 null。 |

## <a name="construct-your-http-request-and-request-body"></a>构造 HTTP 请求和请求正文
既然已经为索引操作收集了必要的字段值，就可以构造实际的 HTTP 请求和 JSON 请求正文来导入数据。

#### <a name="request-and-request-headers"></a>请求和请求头
在 URL 中，需要提供服务名称、索引名称（本例为“hotels”）以及正确的 API 版本（发布本文档时的 API 版本为 `2017-11-11` ）。 需要定义 `Content-Type` 和 `api-key` 请求头。 请使用服务的其中一个管理密钥来定义后者。

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>请求正文
```JSON
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

在本例中，使用 `upload`、`mergeOrUpload` 和 `delete` 来作为搜索操作。

假定“hotels”这一示例索引已填充了许多文档。 注意，使用 `mergeOrUpload` 时不必指定所有可能的文档字段，使用 `delete` 时仅指定文档关键字段 `hotelId`。

另请注意，一个索引请求中最多只能包含 1000 个（或者 16 MB）文档。

## <a name="understand-your-http-response-code"></a>了解 HTTP 响应代码
#### <a name="200"></a>200
成功提交索引请求后，会收到状态代码为 `200 OK`的 HTTP 响应。 HTTP 响应的 JSON 正文如下所示：

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
至少有一项未成功索引时，会返回状态代码 `207` 。 HTTP 响应的 JSON 正文将包含失败文档的信息。

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> 这通常意味着，搜索服务上的负载即将达到某个点，索引请求会在该点开始返回 `503` 响应。 在这种情况下，强烈建议等到客户端代码自动消失，并重试。 这可以给系统一些时间来恢复，增加今后请求成功的可能性。 快速重试请求只会延长这种情况持续的时间。
>
>

#### <a name="429"></a>429
超过每个索引的文档数配额时，会返回状态代码 `429` 。

#### <a name="503"></a>503
如果请求中的所有项均未成功索引，会返回状态代码 `503` 。 此错误表示系统负载过重，当前无法处理请求。

> [!NOTE]
> 在这种情况下，强烈建议等到客户端代码自动消失，并重试。 这可以给系统一些时间来恢复，增加今后请求成功的可能性。 快速重试请求只会延长这种情况持续的时间。
>
>

有关文档操作以及成功/错误响应的详细信息，请参阅 [添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)。 有关请求失败时可能返回的其他 HTTP 状态代码的详细信息，请参阅 [HTTP 状态代码（Azure 搜索）](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)。

## <a name="next-steps"></a>后续步骤
填充 Azure 搜索索引后，即可发出查询，搜索文档。 有关详细信息，请参阅 [查询 Azure 搜索索引](search-query-overview.md) 。
