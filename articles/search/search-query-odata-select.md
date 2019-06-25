---
title: OData 选择参考-Azure 搜索
description: 适用于 OData 语言参考选择在 Azure 搜索查询中的语法。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079673"
---
# <a name="odata-select-syntax-in-azure-search"></a>Azure 搜索中的 OData $select 语法

 可以使用[OData **$select**参数](query-odata-filter-orderby-syntax.md)以选择要从 Azure 搜索的搜索结果中包括哪些字段。 本文介绍的语法 **$select**在详细信息。 有关如何使用更多常规信息 **$select**搜索结果时，请参阅[如何使用搜索结果在 Azure 搜索中](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$Select**参数确定查询结果集中返回哪些字段对每个文档。 以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的语法 **$select**参数：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

**$Select**参数采用两种形式：

1. 星号 (`*`)，指示应返回所有可检索字段，或
1. 以逗号分隔的字段路径的列表，确定哪些字段应返回。

如果使用第二种形式，则仅可能在列表中指定检索的字段。

如果无需显式指定其子字段列出复杂字段，则将在查询结果集包含所有可检索的子字段。 例如，假定索引具有`Address`字段`Street`， `City`，和`Country`所有可检索的子字段。 如果指定`Address`中 **$select**，查询结果将包括所有三个子字段。

## <a name="examples"></a>示例

包括`HotelId`， `HotelName`，并`Rating`顶级字段在结果中，并将`City`的子字段`Address`:

    $select=HotelId, HotelName, Rating, Address/City

示例结果可能如下所示：

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

包括`HotelName`结果中的所有子字段中的顶级字段`Address`，并`Type`并`BaseRate`中每个对象的子字段`Rooms`集合：

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

示例结果可能如下所示：

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中如何使用搜索结果](search-pagination-page-layout.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
