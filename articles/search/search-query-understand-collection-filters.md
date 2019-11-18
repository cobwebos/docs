---
title: 了解 OData 集合筛选器
titleSuffix: Azure Cognitive Search
description: 了解 OData 集合筛选器在 Azure 中的工作原理的机制认知搜索查询，包括集合独有的限制和行为。
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113071"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>了解 Azure 中的 OData 集合筛选器认知搜索

若要对 Azure 认知搜索中的集合字段进行[筛选](query-odata-filter-orderby-syntax.md)，可以将[`any` 和 `all` 运算符](search-query-odata-collection-operators.md)与**lambda 表达式**一起使用。 Lambda 表达式是引用**范围变量**的布尔表达式。 `any` 和 `all` 运算符类似于大多数编程语言中的 `for` 循环，范围变量充当循环变量的角色，而 Lambda 表达式充当循环的主体。 范围变量在循环迭代期间采用集合的“当前”值。

最起码，这在概念上解释了此类筛选器的工作原理。 事实上，Azure 认知搜索以非常不同的方式实现筛选器，以实现 `for` 循环的工作方式。 理想情况下，你察觉不到这种差异，但在某些情况下你会感受到这种差异。 最终结果是，在编写 Lambda 表达式时必须遵循某些规则。

本文介绍如何通过探索 Azure 认知搜索如何执行这些筛选器来提供集合筛选器的规则。 如果你正在使用复杂的 Lambda 表达式编写高级筛选器，本文可以帮助你了解筛选器中支持哪些元素，以及使用这些元素的原因。

有关集合筛选器规则的信息（包括示例），请参阅[Azure 中的 OData 集合筛选器疑难解答认知搜索](search-query-troubleshoot-collection-filters.md)。

## <a name="why-collection-filters-are-limited"></a>集合筛选器为何受到限制

出于以下三个根本原因，并非所有筛选器功能都受所有集合类型的支持：

1. 特定的数据类型仅支持特定的运算符。 例如，使用 `true`、`false` 等运算符比较布尔值 `lt` 和 `gt` 是没有意义的。
1. Azure 认知搜索不支持对 `Collection(Edm.ComplexType)`类型的字段进行**相关搜索**。
1. Azure 认知搜索使用反转索引对所有类型的数据（包括集合）执行筛选器。

第一个原因只是 OData 语言和 EDM 类型系统的定义方式造成的。 本文的余下部分将更详细地解释后两个原因。

## <a name="correlated-versus-uncorrelated-search"></a>关联搜索与非关联搜索

对复杂对象集合应用多个筛选条件时，这些条件是**关联的**，因为它们将应用到*集合中的每个对象*。 例如，以下筛选器将返回至少提供一间价格低于 100 的豪华客房的酒店：

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

如果筛选*不相关联*，上述筛选器可能会返回提供一间豪华客房并提供基本价格低于 100 的另一间客房的酒店。 该结果没有任何意义，因为 Lambda 表达式的两个子句将应用到同一个范围变量，即 `room`。 这就是此类筛选器相关联的原因。

但是，对于全文搜索，无法引用特定的范围变量。 如果使用字段搜索发出如下所示的[完整 Lucene 查询](query-lucene-syntax.md)：

    Rooms/Type:deluxe AND Rooms/Description:"city view"

可能会返回提供一间豪华客房，并在另一间客房的描述中提到“市景”的酒店。 例如，以下 `Id` 为 `1` 的文档将与查询相匹配：

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

原因在于，`Rooms/Type` 引用了整个文档中 `Rooms/Type` 字段的所有已分析字词，`Rooms/Description` 与此类似，如下表所示。

如何为全文搜索存储 `Rooms/Type`：

| `Rooms/Type` 中的字词 | 文档 ID |
| --- | --- |
| 豪华 | 1, 2 |
| standard | 1 |

如何为全文搜索存储 `Rooms/Description`：

| `Rooms/Description` 中的字词 | 文档 ID |
| --- | --- |
| 庭院 | 2 |
| city | 1 |
| 花园 | 1 |
| 大 | 1 |
| 汽车旅馆 | 2 |
| 客房 | 1, 2 |
| standard | 1 |
| 套间 | 1 |
| view | 1 |

简单而言，上述筛选器指出“匹配其中某间客房的 `Type` 等于‘豪华客房’，且**同一间客房** 的 `BaseRate` 小于 100 的文档”，而该搜索查询则与此不同，它指出“匹配其中的 `Rooms/Type` 包含字词‘豪华’且 `Rooms/Description` 包含短语‘市景’的文档”。 对于后面的查询，可关联哪些客房的字段没有概念。

> [!NOTE]
> 如果你想要查看添加到 Azure 认知搜索的相关搜索支持，请投票[此用户语音项目](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)。

## <a name="inverted-indexes-and-collections"></a>倒排索引和集合

你可能已注意到，基于复杂集合的 Lambda 表达式的限制，要比基于简单集合（例如 `Collection(Edm.Int32)`、`Collection(Edm.GeographyPoint)` 等）的表达式的限制要少得多。 这是因为 Azure 认知搜索会将复杂的集合存储为子文档的实际集合，而简单集合根本不会存储为集合。

例如，假设某个在线零售商的索引中包含类似于 `seasons` 的可筛选字符串集合字段。 上传到此索引的某些文档可能如下所示：

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

`seasons` 字段的值存储在称作“倒排索引”的结构中，如下所示：

| 术语 | 文档 ID |
| --- | --- |
| 春 | 1, 2 |
| 夏 | 1 |
| 秋 | 1, 2 |
| 冬 | 2, 3 |

此数据结构旨在以极佳的速度回答一个问题：指定字词出现在哪些文档中？ 解答此问题的过程更像是一个普通的相等性检查，而不是基于集合的循环。 事实上，这就是为什么要使用字符串集合，但 Azure 认知搜索仅允许 `eq` 在 `any`lambda 表达式中使用比较运算符。

基于相等性，接下来我们将探讨如何使用 `or` 来合并多个针对同一范围变量执行的相等性检查。 此方法得益于代数原理以及[限定符的分布属性](https://en.wikipedia.org/wiki/Existential_quantification#Negation)。 此表达式：

    seasons/any(s: s eq 'winter' or s eq 'fall')

等效于：

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

可以使用倒排索引有效执行两个 `any` 子表达式中的每一个。 另外，得益于[限定符的求反法则](https://en.wikipedia.org/wiki/Existential_quantification#Negation)，此表达式：

    seasons/all(s: s ne 'winter' and s ne 'fall')

等效于：

    not seasons/any(s: s eq 'winter' or s eq 'fall')

正因如此，我们可以将 `all` 与 `ne` 和 `and` 一起使用。

> [!NOTE]
> 尽管本文档并未详细介绍，但相同的原理也可以延伸到[地理空间点集合的距离和交集测试](search-query-odata-geo-spatial-functions.md)。 因此，在 `any` 中：
>
> - `geo.intersects` 不可求反
> - 必须使用 `geo.distance` 或 `lt` 比较 `le`
> - 必须使用 `or` 而不是 `and` 合并表达式
>
> 相反的规则适用于 `all`。

在对支持 `lt`、`gt`、`le` 和 `ge` 运算符的数据类型集合进行筛选时，可以使用更多种表达式，例如 `Collection(Edm.Int32)`。 具体而言，可以在 `and` 中使用 `or` 以及 `any`，前提是使用  **将基础比较表达式合并到**范围比较`and`，然后使用 `or` 进一步合并。 这种布尔表达式结构称为[析取范式 (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)，也称为“AND 的 OR”。 相反，这些数据类型的 `all` Lambda 表达式必须采用[合取范式 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)，也称为“OR 的 AND”。 Azure 认知搜索允许进行此类范围的比较，因为它可以有效地使用反转索引执行这些比较，就像它能对字符串执行快速字词查找一样。

下面汇总了有关 Lambda 表达式中允许的元素的经验法则：

- 在 `any` 中，始终允许*正检查*，例如相等性、范围比较、`geo.intersects`，或者 `geo.distance` 与 `lt` 或 `le` 的比较（在检查距离时，将“靠近程度”视为相等性）。
- 在 `any` 中，始终允许 `or`。 只能对可以表达范围检查的数据类型使用 `and`，并且只能在使用“AND 的 OR”(DNF) 时使用它。
- 在 `all` 中，规则将会反转 - 只允许*负检查*，始终可以使用 `and`，并只能对表达为“OR 的 AND”(CNF) 的范围检查使用 `or`。

在实践中，你最有可能使用这些筛选器类型。 不过，了解筛选器选项的界限仍有帮助。

有关允许和不允许的筛选器类型的具体示例，请参阅[如何编写有效的集合筛选器](search-query-troubleshoot-collection-filters.md#bkmk_examples)。

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的 OData 集合筛选器疑难解答](search-query-troubleshoot-collection-filters.md)
- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
