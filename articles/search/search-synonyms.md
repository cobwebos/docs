---
title: 用于在搜索索引上进行查询扩展的同义词
titleSuffix: Azure Cognitive Search
description: 创建同义词映射，以扩展 Azure 认知搜索索引上搜索查询的作用域。 扩宽了范围，使其包括你在列表中提供的同义术语。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194336"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure 认知搜索中的同义词

搜索引擎中的同义词功能无需用户实际提供术语，便可关联隐式扩展查询作用域的等效术语。 例如，若给定术语“dog”以及“canine”和“puppy”同义词关联，则包含“dog”、“canine”或“puppy”的所有文档都属于查询作用域。

在 Azure 认知搜索中，将在查询时完成同义词扩展。 可将同义词映射添加到服务，而不会中断现有操作。 可将  **synonymMaps** 属性添加到字段定义，而无需重新生成索引。

## <a name="create-synonyms"></a>创建同义词

无门户支持来创建同义词，但你可以使用 REST API 或 .NET SDK。 若要开始使用 REST，建议使用以下 API 的[Postman](search-get-started-postman.md)和表述请求： [Create 同义词 Maps](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)。 对于C#开发人员，你可以[使用C#在 Azure 认知搜索中开始添加同义词](search-synonyms-tutorial-sdk.md)。

（可选）如果使用[客户托管的密钥](search-security-manage-encryption-keys.md)进行服务端加密，则可以将该保护应用于同义词映射的内容。

## <a name="use-synonyms"></a>使用同义词

在 Azure 认知搜索中，同义词支持基于你定义和上传到你的服务的同义词映射。 这些映射构成独立的资源（如索引或数据源），在搜索服务中可用于任何索引的任何可搜索字段。

同义词映射和索引独立维护。 定义同义词映射并将其上传到服务后，可通过在字段定义中添加名为 **synonymMaps** 的新属性在字段上启用同义词功能。 创建、更新和删除同义词映射始终是一项全文档操作。也就是说，无法逐个创建、更新或删除同义词映射的各个部分。 甚至更新单个条目也需要重新加载。

将同义词并入搜索应用程序需要两步：

1.  通过以下 API 将同义词映射添加到搜索服务。  

2.  配置可搜索字段以在索引定义中使用同义词映射。

可为搜索应用程序创建多个同义词映射（例如，如果应用程序支持多语言客户群，则可按语言创建同义词映射）。 目前，一个字段仅可使用其中一种。 可随时更新字段的 synonymMaps 属性。

### <a name="synonymmaps-resource-apis"></a>SynonymMaps 资源 API

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>使用 POST 或 PUT 可在服务下添加或更新同义词映射。

使用 POST 或 PUT 可将同义词映射上传到服务。 每个规则必须通过换行符（“\n”）进行分隔。 在免费服务中，最多可以为每个同义词映射定义5000个规则，在所有其他 Sku 中定义20000个规则。 每条规则可包含最多 20 个扩展。

同义词映射的格式必须为 Apache Solr，以下对此进行了解释。 如果现有的同义词字典具有不同格式，并且希望直接使用它，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 上向我们反馈。

如以下示例所示，可使用 HTTP POST 创建新的同义词映射：

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

此外，可使用 PUT 并在 URI 上指定同义词映射名称。 如果同义词映射不存在，则创建一个。

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr 同义词格式

Solr 格式支持等效和显式同义词映射。 映射规则遵循 Apache Solr 的开源同义词筛选器规范，如本文档中所述： [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter)。 下面是等效同义词的示例规则。
```
USA, United States, United States of America
```

使用以上规则，搜索查询“USA”会扩展为“USA”、“United States”或“United States of America”。

箭头“=>”表示显式映射。 指定时，与 "= >" 左侧匹配的搜索查询的字词序列将替换为右侧的替代项。 给定以下规则，搜索查询“Washington”、“Wash”。 或“WA”全都会重写为“WA”。 显式映射只会按指定方向应用，在此示例中，不会将查询“WA”重写为“Washington”。
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>列出服务下的同义词映射。

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>获取服务下的同义词映射。

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>删除服务下的同义词映射。

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>配置可搜索字段以在索引定义中使用同义词映射。

新字段属性 **synonymMaps** 可用于指定同义词映射以供可搜索字段使用。 同义词映射是服务级资源，服务下的任意索引字段都可以引用。

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

可为类型“Edm.String”或“Collection(Edm.String)”的可搜索字段指定 **synonymMaps**。

> [!NOTE]
> 每个字段仅可包含一个同义词映射。 如果要使用多个同义词映射，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 上告诉我们。

## <a name="impact-of-synonyms-on-other-search-features"></a>同义词功能对其他搜索功能的影响

同义词功能将使用 OR 操作符将原始查询重写为同义词。 出于这个原因，突出显示和计分配置文件会将原始术语和同义词视为等效项。

同义词功能适用于搜索查询且不适用于筛选器或方面。 同样，建议仅基于原始术语；同义词匹配不会在响应中显示。

同义词扩展不适用于通配符搜索术语；也不会扩展前缀、模糊和正则表达式术语。

如果需要执行应用同义词扩展和通配符、正则表达式或模糊搜索的单个查询，则可以使用 OR 语法组合查询。 例如，若要将同义词与通配符组合用于简单查询语法，则术语将为 `<query> | <query>*`。

如果开发（非生产）环境中具有现有索引，请使用一个小字典进行试验，了解添加同义词如何更改搜索体验，包括对计分配置文件、突出显示和建议造成的影响。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建同义词映射](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)