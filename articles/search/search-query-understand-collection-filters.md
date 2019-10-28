---
title: 了解 OData 集合筛选器
titleSuffix: Azure Cognitive Search
description: 了解 OData 集合筛选器在 Azure 认知搜索查询中的工作原理。
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
ms.openlocfilehash: 9a57e1d16b13d822b6f5b541a7f838b0dd3a69ad
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794397"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>了解 Azure 中的 OData 集合筛选器认知搜索

若要对 Azure 认知搜索中的集合字段进行[筛选](query-odata-filter-orderby-syntax.md)，可以将[`any` 和 `all` 运算符](search-query-odata-collection-operators.md)与**lambda 表达式**一起使用。 Lambda 表达式是引用**范围变量**的布尔表达式。 `any` 和 `all` 运算符类似于大多数编程语言中的 `for` 循环，范围变量采用循环变量的角色，而 lambda 表达式作为循环体。 范围变量在循环迭代期间采用集合的 "当前" 值。

至少在概念上是它的工作原理。 事实上，Azure 认知搜索以非常不同的方式实现筛选器，以实现 `for` 循环的工作方式。 理想情况下，这种差异对您是不可见的，但在某些情况下不会出现。 最终的结果是，编写 lambda 表达式时必须遵循的规则。

本文介绍如何通过探索 Azure 认知搜索如何执行这些筛选器来提供集合筛选器的规则。 如果你正在用复杂的 lambda 表达式编写高级筛选器，你可能会发现这篇文章有助于构建你对筛选器和原因的了解。

有关集合筛选器规则的信息（包括示例），请参阅[Azure 中的 OData 集合筛选器疑难解答认知搜索](search-query-troubleshoot-collection-filters.md)。

## <a name="why-collection-filters-are-limited"></a>为何收集筛选器受到限制

对于所有类型的集合，不支持所有筛选器功能，有三个根本原因：

1. 某些数据类型仅支持某些运算符。 例如，使用 `lt`、`gt`等比较 `true` 和 `false` 的布尔值是有意义的。
1. Azure 认知搜索不支持对 `Collection(Edm.ComplexType)`类型的字段进行**相关搜索**。
1. Azure 认知搜索使用反转索引对所有类型的数据（包括集合）执行筛选器。

第一个原因是如何定义 OData 语言和 EDM 类型系统的结果。 本文的其余部分将更详细地介绍最后两个。

## <a name="correlated-versus-uncorrelated-search"></a>相关与不相关搜索

当对复杂对象集合应用多个筛选条件时，这些条件会**相关**，因为它们应用于*集合中的每个对象*。 例如，以下筛选器将返回至少具有一个速率小于100的高级房间的酒店：

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

如果筛选是不相关的，则上述筛选器可能会返回*酒店，其中*一个房间为高级，另一个房间的基本费率小于100。 这并没有任何意义，因为 lambda 表达式的两个子句都适用于同一范围变量，即 `room`。 这就是将此类筛选器相关的原因。

但是，对于全文搜索，无法引用特定的范围变量。 如果使用现场 search 发出[完整的 Lucene 查询](query-lucene-syntax.md)，如下所示：

    Rooms/Type:deluxe AND Rooms/Description:"city view"

你可能会收到一条饭店，其中一个房间是高级的，而另一个房间提到说明中的 "city view"。 例如，下面带有 `1` `Id` 的文档将与查询相匹配：

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

原因是 `Rooms/Type` 指的是整个文档中 `Rooms/Type` 字段的所有已分析的术语，与 `Rooms/Description`类似，如下表所示。

如何为全文搜索存储 `Rooms/Type`：

| `Rooms/Type` 中的术语 | 文档 Id |
| --- | --- |
| 高级 | 1, 2 |
| 标准 | 第 |

如何为全文搜索存储 `Rooms/Description`：

| `Rooms/Description` 中的术语 | 文档 Id |
| --- | --- |
| courtyard | 2 |
| city | 第 |
| 平面 | 第 |
| 大型 | 第 |
| 汽车旅馆 | 2 |
| 留下 | 1, 2 |
| 标准 | 第 |
| 强大 | 第 |
| 视图 | 第 |

与上面的筛选器不同，这基本上说 "匹配会议室已 `Type` 等于 ' 高级房间 ' 的文档，并且**同一房间**`BaseRate` 小于 100"，搜索查询会显示 "匹配文档，其中 `Rooms/Type` 包含术语" 高级 "和 `Rooms/Description`具有 "city view" 字样。 对于其字段在后一种情况下可以关联的单独房间，没有任何概念。

> [!NOTE]
> 如果你想要查看添加到 Azure 认知搜索的相关搜索支持，请投票[此用户语音项目](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)。

## <a name="inverted-indexes-and-collections"></a>反转索引和集合

您可能已注意到，对于复杂的集合而言，对 lambda 表达式的限制比简单集合（如 `Collection(Edm.Int32)`、`Collection(Edm.GeographyPoint)`等）的限制更少。 这是因为 Azure 认知搜索会将复杂的集合存储为子文档的实际集合，而简单集合根本不会存储为集合。

例如，请考虑一个可筛选的字符串集合字段，例如 `seasons` 在联机零售商的索引中。 上载到此索引的某些文档可能如下所示：

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

`seasons` 字段的值存储在名为**反转索引**的结构中，如下所示：

| 条款 | 文档 Id |
| --- | --- |
| 草绿色 | 1, 2 |
| 夏季 | 第 |
| 明显 | 1, 2 |
| 世界 | 2，3 |

此数据结构旨在以极佳的速度回答一个问题：指定字词出现在哪些文档中？ 对此问题的回答比对集合的循环更像是一个简单的相等检查。 事实上，这就是为什么要使用字符串集合，但 Azure 认知搜索仅允许 `eq` 在 `any`lambda 表达式中使用比较运算符。

根据相等性，接下来我们将介绍如何将同一范围变量上的多个相等检查与 `or`组合在一起。 它的工作方式得益于代数和[限定符的分配律](https://en.wikipedia.org/wiki/Existential_quantification#Negation)。 此表达式：

    seasons/any(s: s eq 'winter' or s eq 'fall')

等效于：

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

每个 `any` 子表达式都可以使用反转索引来有效地执行。 另外，由于[限定符的求反定律](https://en.wikipedia.org/wiki/Existential_quantification#Negation)，此表达式如下所示：

    seasons/all(s: s ne 'winter' and s ne 'fall')

等效于：

    not seasons/any(s: s eq 'winter' or s eq 'fall')

这就是为什么可以将 `all` 与 `ne` 和 `and`结合使用的原因。

> [!NOTE]
> 虽然详细信息超出了本文档的范围，但这些相同的原则还会延伸到[地理空间点集合的距离和交集测试](search-query-odata-geo-spatial-functions.md)。 这就是 `any`中的原因：
>
> - 不能取反 `geo.intersects`
> - `geo.distance` 必须使用 `lt` 或 `le` 进行比较
> - 表达式必须与 `or`组合在一起，而不能 `and`
>
> 相反规则适用于 `all`。

筛选支持 `lt`、`gt`、`le`和 `ge` 运算符的数据类型集合时，可以使用更多的表达式，例如 `Collection(Edm.Int32)`。 具体而言，你可以在 `any`中使用 `and` 和 `or`，前提是基础比较表达式合并为使用 `and`的**范围比较**，然后使用 `or`进一步组合。 此布尔表达式的结构称为[析取范式 Normal Form （DNF）](https://en.wikipedia.org/wiki/Disjunctive_normal_form)，也称为 "Or of ANDs"。 相反，针对这些数据类型的 `all` 的 lambda 表达式必须是[联合 Normal 形式（.cnf）](https://en.wikipedia.org/wiki/Conjunctive_normal_form)，也称为 "ANDs of or"。 Azure 认知搜索允许进行此类范围的比较，因为它可以有效地使用反转索引执行这些比较，就像它能对字符串执行快速字词查找一样。

总而言之，以下是 lambda 表达式中允许的内容的经验法则：

- 在 `any`中，始终允许进行*正面检查*，例如相等性、范围比较、`geo.intersects`或 `geo.distance` 与 `lt` 或 `le` 比较（将 "靠近程度" 视为检查距离时，将其视为等效）。
- 在 `any`中，始终允许使用 `or`。 仅当使用 ANDs （DNF）的 Or 时，才能将 `and` 仅用于可快速进行范围检查的数据类型。
- 在 `all`中，将反转规则--仅允许*负检查*，你可以使用 `and` always，并且只能将 `or` 用于表示为 OR （.cnf）的 ANDs 的范围检查。

在实际情况下，这些是你最可能使用的筛选器类型。 但仍有必要了解的边界。

有关允许和不允许的筛选器类型的特定示例，请参阅[如何编写有效的集合筛选器](search-query-troubleshoot-collection-filters.md#bkmk_examples)。

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的 OData 集合筛选器疑难解答](search-query-troubleshoot-collection-filters.md)
- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
