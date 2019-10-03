---
title: OData 选择引用-Azure 搜索
description: Azure 搜索查询中 select 语法的 OData 语言参考。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647876"
---
# <a name="odata-select-syntax-in-azure-search"></a>Azure 搜索中的 OData $select 语法

 可以使用[OData **$select**参数](query-odata-filter-orderby-syntax.md)选择要包含在 Azure 搜索的搜索结果中的字段。 本文详细介绍 **$select**的语法。 有关如何在显示搜索结果时使用 **$select**的更多常规信息, 请参阅[如何在 Azure 搜索中使用搜索结果](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$Select**参数确定在查询结果集中返回每个文档的哪些字段。 以下 EBNF ([扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义 **$select**参数的语法:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

还提供交互式语法关系图:

> [!div class="nextstepaction"]
> [适用于 Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 有关完整的 EBNF, 请参阅[Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)。

**$Select**参数分为两种形式:

1. 单个星号 (`*`), 指示应返回所有可检索字段, 或
1. 以逗号分隔的字段路径列表, 用于标识应返回哪些字段。

使用第二种形式时, 只能在列表中指定可检索的字段。

如果您在未显式指定其子字段的情况下列出了复杂字段, 则所有可检索的子字段都将包含在查询结果集中。 例如, 假设您的索引有一个`Address`字段, `Street`其中`City`包含、 `Country`和子字段, 它们均可检索。 如果在`Address` **$select**中指定, 查询结果将包含所有三个子字段。

## <a name="examples"></a>示例

`HotelName` `Rating` 在结果`Address`中包括`HotelId`、和顶级字段, 以及的子字段: `City`

    $select=HotelId, HotelName, Rating, Address/City

示例结果可能如下所示:

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

`BaseRate` `Address` `Type`在结果中包含`Rooms`顶级字段以及的所有子字段, 以及集合中每个对象的和子字段: `HotelName`

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

示例结果可能如下所示:

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

- [如何在 Azure 搜索中使用搜索结果](search-pagination-page-layout.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
