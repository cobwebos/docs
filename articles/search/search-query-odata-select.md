---
title: OData 选择引用
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索查询中的 select 语法的 OData 语言参考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 7786974f3d39f9cbc81e1ffea955156d623f1476
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793249"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure 中的 OData $select 语法认知搜索

 可以使用[OData **$select**参数](query-odata-filter-orderby-syntax.md)选择要包含在 Azure 认知搜索的搜索结果中的字段。 本文详细介绍 **$select**的语法。 有关如何在显示搜索结果时使用 **$select**的更多常规信息，请参阅[如何使用 Azure 认知搜索中的搜索结果](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$Select**参数确定在查询结果集中返回每个文档的哪些字段。 以下 EBNF （[扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义 **$select**参数的语法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

**$Select**参数分为两种形式：

1. 单个星号（`*`），指示应返回所有可检索字段，或
1. 以逗号分隔的字段路径列表，用于标识应返回哪些字段。

使用第二种形式时，只能在列表中指定可检索的字段。

如果您在未显式指定其子字段的情况下列出了复杂字段，则所有可检索的子字段都将包含在查询结果集中。 例如，假设您的索引具有一个 `Address` 字段，其中 `Street`、`City`和 `Country` 子字段均可检索。 如果在 **$select**中指定 `Address`，则查询结果将包括所有三个子字段。

## <a name="examples"></a>示例

在结果中包含 `HotelId`、`HotelName`和 `Rating` 顶级字段，以及 `Address`的 `City` 子字段：

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

在结果中包括 `HotelName` 的顶级字段，以及 `Address`的所有子字段，以及 `Rooms` 集合中每个对象的 `Type` 和 `BaseRate` 子字段：

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

- [如何在 Azure 认知搜索中使用搜索结果](search-pagination-page-layout.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
