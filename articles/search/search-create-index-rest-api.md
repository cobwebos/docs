---
title: "使用 REST API 创建 Azure 搜索索引 | Microsoft Docs"
description: "使用 Azure 搜索 HTTP REST API 在代码中创建索引。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: ac6c5fba-ad59-492d-b715-d25a7a7ae051
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 6ff31940f3a4e7557e0caf3d9d3740590be3bc04
ms.openlocfilehash: 6d3bbea1a891e1d2f41eedccd9b9a591dfe13855


---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>使用 REST API 创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-what-is-an-index.md)
> * [门户](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

本文介绍了如何使用 Azure 搜索 REST API 创建 Azure 搜索 [索引](https://msdn.microsoft.com/library/azure/dn798941.aspx) 。

按照本指南创建索引之前，应已经 [创建 Azure 搜索服务](search-create-service-portal.md)。

若要使用 REST API 创建 Azure 搜索索引，需向 Azure 搜索服务的 URL 终结点发出一个 HTTP POST 请求。 索引定义作为格式标准的 JSON 内容包含在请求正文中。

## <a name="i-identify-your-azure-search-services-admin-apikey"></a>I. 确定 Azure 搜索服务的管理 API 密钥
现在，已预配 Azure 搜索服务，可以使用 REST API 向服务的 URL 终结点发出 HTTP 请求。 但是，所有  API 请求必须包含为预配的搜索服务所生成的 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

1. 若要查找服务的 API 密钥，必须登录到 [Azure 门户](https://portal.azure.com/)
2. 转到 Azure 搜索服务的边栏选项卡
3. 单击“密钥”图标

服务将具有*管理密钥*和*查询密钥*。

* 主管理密钥和辅助 *管理密钥* 授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。 有两个密钥的作用是确保在决定重新生成主密钥时可以继续使用辅助密钥，反之亦然。
* *查询密钥* 授予对索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。

可以使用主管理密钥或辅助管理密钥来创建索引。

## <a name="ii-define-your-azure-search-index-using-wellformed-json"></a>II. 使用格式标准的 JSON 定义 Azure 搜索索引
对服务的单个 HTTP POST 请求将创建索引。 HTTP POST 请求的正文包含一个定义 Azure 搜索索引的 JSON 对象。

1. 此 JSON 对象的第一个属性是索引的名称。
2. 此 JSON 对象的第二个属性是一个名为 `fields` 的 JSON 数组，其包含索引中每个字段的一个单独 JSON 对象。 每个 JSON 对象包含每个字段属性（包括“名称”、“类型”等）的多个名称/值对。

设计索引时，必须牢记搜索用户体验和业务需求，因为必须为每个字段分配 [适当的属性](https://msdn.microsoft.com/library/azure/dn798941.aspx)。 这些属性控制何种搜索功能（筛选、分面、排序全文搜索等）应用于哪个字段。 对于任何未指定的属性，除非专门禁用默认设置，否则系统将默认启动相应的搜索功能。

此示例中，索引命名为“hotels”，字段按如下方式定义：

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

根据每个字段在应用程序中的预期使用方式，谨慎选择其索引属性。 例如，用户搜索酒店时很可能不知道 `hotelId` 这一唯一键，因此通过将 `searchable` 设置为 `false`（可节省索引空间），禁用该字段的全文搜索。

请注意，必须将类型为 `Edm.String` 的索引中的确切字段指定为“密钥”字段。

上述索引定义对 `description_fr` 字段使用了自定义语言分析器，因为它用于存储法语文本。 请参阅 [MSDN 上的语言支持](https://msdn.microsoft.com/library/azure/dn879793.aspx)主题以及相应的[博客文章](https://azure.microsoft.com/blog/language-support-in-azure-search/)，了解有关语言分析器的详细信息。

## <a name="iii-issue-the-http-request"></a>III. 发出 HTTP 请求
1. 将索引定义用作请求正文，向 Azure 搜索服务终结点 URL 发出 HTTP POST 请求。 在 URL 中，请务必使用服务名称作为主机名，并将适当的 `api-version` 作为查询字符串参数（发布此文档时，API 版本为 `2015-02-28`）。
2. 在请求头中，指定 `Content-Type` 作为 `application/json`。 还需提供步骤 I 中在 `api-key` 标头中标识的服务管理密钥。

必须提供自己的服务名称和 API 密钥，以发出以下请求：

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


对于成功的请求，应看到状态代码 201（已创建）。 有关通过 REST API 创建索引的详细信息，请访问 [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx)上的 API 参考。 有关请求失败时可能返回的其他 HTTP 状态代码的详细信息，请参阅 [HTTP 状态代码（Azure 搜索）](https://msdn.microsoft.com/library/azure/dn798925.aspx)。

使用完索引并想删除它时，只需发出 HTTP DELETE 请求。 例如，以下是删除“hotels”索引的方法：

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>下一步
创建 Azure 搜索索引后，就可以 [将内容上载到索引中](search-what-is-data-import.md) ，以便可以开始搜索数据。




<!--HONumber=Nov16_HO2-->


