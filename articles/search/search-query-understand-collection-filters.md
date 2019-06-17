---
title: 了解 Azure 搜索的 OData 集合筛选器-
description: 了解 OData 集合筛选器在 Azure 搜索查询中的工作原理。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079595"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>了解 Azure 搜索中的 OData 集合筛选器

向[筛选器](query-odata-filter-orderby-syntax.md)在 Azure 搜索中的集合字段，可以使用[`any`并`all`运算符](search-query-odata-collection-operators.md)一起使用**lambda 表达式**。 Lambda 表达式是指代的布尔表达式**范围变量**。 `any`并`all`运算符是类似于`for`中大多数编程语言，采用循环变量和 lambda 表达式作为循环主体的角色的范围变量循环。 范围变量将在循环迭代集合的"当前"值。

那时，至少是工作方式从概念上讲。 在实际情况下，Azure 搜索筛选器实现中非常不同的方式对如何`for`循环工作。 理想情况下，这种差异是不可见的但在某些情况下它不是。 最终结果是有您需要编写 lambda 表达式时遵循的规则。

本文介绍了为什么集合筛选器的规则存在通过探索 Azure 搜索如何执行这些筛选器。 如果您要编写使用复杂的 lambda 表达式的高级筛选器，您可能会发现这篇文章有助于构建了解什么是可以在筛选器和原因。

有关规则的集合筛选器，包括示例中，看到的内容的信息[Azure 搜索中的故障排除 OData 集合筛选器](search-query-troubleshoot-collection-filters.md)。

## <a name="why-collection-filters-are-limited"></a>为什么集合筛选器是有限

有三个基本原因为什么不所有筛选器功能支持所有类型的集合：

1. 只有某些运算符支持的特定数据类型。 例如，它没有意义的布尔值进行比较`true`并`false`使用`lt`， `gt`，依次类推。
1. Azure 搜索不支持**相关的搜索**类型的字段上`Collection(Edm.ComplexType)`。
1. Azure 搜索使用倒排索引以执行数据，包括集合的所有类型的筛选器。

第一个原因是只是如何定义 OData 语言和 EDM 类型系统的结果。 在本文的其余部分更详细地介绍了最后两个。

## <a name="correlated-versus-uncorrelated-search"></a>与不相关搜索相关联

在将多个筛选条件应用对复杂对象的集合，条件是**相关**因为它们适用于*集合中的每个对象*。 例如，以下筛选器将返回具有至少一个高级空间速率小于 100 的酒店：

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

如果筛选*无关联*，上面的筛选器可能会返回的酒店，其中一个房间是高级，另一个房间的 base 速率小于 100。 这并不合理，因为 lambda 表达式的两个子句应用于相同的范围变量，即`room`。 这是此类筛选器相关的原因。

但是，对于全文搜索，没有办法对特定范围变量，请参阅。 如果您使用现场的搜索颁发[完整 Lucene 查询](query-lucene-syntax.md)类似此：

    Rooms/Type:deluxe AND Rooms/Description:"city view"

在说明中，可能会收到的酒店返回其中一个房间是高级，而另一个房间提及"市/县视图"。 例如，下面的文档与`Id`的`1`与查询匹配：

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

其原因在于`Rooms/Type`引用的所有已分析字词`Rooms/Type`字段对于整个文档，也同样如此`Rooms/Description`下, 表中所示。

如何`Rooms/Type`为全文搜索存储：

| 中的术语 `Rooms/Type` | 文档 Id |
| --- | --- |
| 高级 | 1, 2 |
| standard | 第 |

如何`Rooms/Description`为全文搜索存储：

| 中的术语 `Rooms/Description` | 文档 Id |
| --- | --- |
| 庭院 | 2 |
| city | 第 |
| 园 | 第 |
| 大型 | 第 |
| 汽车旅馆 | 2 |
| 聊天室 | 1, 2 |
| standard | 第 |
| 套件 | 第 |
| view | 第 |

因此与上面的筛选器，不同的基本含义是"匹配其中一个房间都有的文档`Type`等于豪华房间和**该同一房间**具有`BaseRate`少于 100 个"，搜索查询就是"匹配文档的位置`Rooms/Type`使用了"高级"条款和`Rooms/Description`包含短语"市/县视图"。 没有可在后一种情况中相关字段的单个聊天室的概念。

> [!NOTE]
> 如果你想要查看支持添加到 Azure 搜索的相关搜索，请投票[此 User Voice 项](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)。

## <a name="inverted-indexes-and-collections"></a>倒排的索引和集合

您可能已经注意到有比简单的集合，例如要少得多 lambda 表达式通过复杂集合限制`Collection(Edm.Int32)`， `Collection(Edm.GeographyPoint)`，依次类推。 这是因为 Azure 搜索作为子文档的实际集合存储复杂集合，而简单集合不在所有存储作为集合。

例如，考虑一个可筛选的字符串集合字段，如`seasons`中在线零售商的索引。 上传到此索引的某些文档可能如下所示：

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

值`seasons`字段存储在名为结构**倒排索引**，其外观如下所示：

| 术语 | 文档 Id |
| --- | --- |
| Spring | 1, 2 |
| 年夏季 | 第 |
| fall | 1, 2 |
| 冬季 | 2, 3 |

此数据结构旨在回答一个问题与很快的速度：中的文档会给定的字词显示？ 回答这个问题工作原理更类似于一个循环是普通的相等性检查对集合。 实际上，这是针对字符串集合的原因，Azure 搜索仅允许`eq`为比较运算符的 lambda 表达式中`any`。

生成从相等性，接下来我们将介绍如何是可以合并多个具有相同的范围变量上的相等性检查`or`。 衷心感谢代数其工作原理并[限定符的分布式属性](https://en.wikipedia.org/wiki/Existential_quantification#Negation)。 此表达式：

    seasons/any(s: s eq 'winter' or s eq 'fall')

等效于：

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

和两个`any`子表达式可以有效地执行使用倒排的索引。 感谢为此外，[求反运算法则限定符](https://en.wikipedia.org/wiki/Existential_quantification#Negation)，此表达式：

    seasons/all(s: s ne 'winter' and s ne 'fall')

等效于：

    not seasons/any(s: s eq 'winter' or s eq 'fall')

正因如此，则可以使用`all`与`ne`和`and`。

> [!NOTE]
> 虽然超出了本文档的范围的详细信息，这些相同的原则扩展到[距离和相交地理空间点的集合的测试](search-query-odata-geo-spatial-functions.md)也。 在正因如此， `any`:
>
> - `geo.intersects` 不能进行求反
> - `geo.distance` 必须使用比较`lt`或 `le`
> - 表达式必须结合使用`or`，而不 `and`
>
> 反向规则适用于`all`。

筛选多个数据集合类型的支持时允许使用表达式的更多种类`lt`， `gt`， `le`，和`ge`运算符，如`Collection(Edm.Int32)`为例。 具体而言，可以使用`and`，以及`or`中`any`，只要基础比较表达式合并到**范围比较**使用`and`，就更多使用组合`or`。 这种结构的布尔表达式称为[析取范式 (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)，也称为"ORs ANDs"。 相反，lambda 表达式`all`对这些数据类型必须位于[合正常窗体 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)，也称为"ORs And"。 Azure 搜索允许此类范围比较，因为它可以执行它们高效地使用反转索引，就像它可以执行快速的字词查找的字符串。

总之，下面是用于 lambda 表达式中允许的内容的经验规则：

- 内部`any`，*正检查*，仍始终允许如相等，范围比较`geo.intersects`，或`geo.distance`与比较`lt`或`le`（就像"靠近程度"视为相等性检查距离时）。
- 内部`any`，`or`始终允许。 可以使用`and`仅对于数据类型可以表示范围检查，且仅当你使用 And Or (DNF)。
- 内部`all`，规则进行了互换-仅*负检查*允许，可以使用`and`往常一样，您可以使用`or`仅为范围检查表示为 And 的 Or (CNF)。

在实践中，这些是您最可能仍要使用的筛选器的类型。 它是仍很有帮助，若要了解什么是可能但的边界。

有关允许哪些类型的筛选器，这不是特定示例，请参阅[如何编写有效的集合筛选器](search-query-troubleshoot-collection-filters.md#bkmk_examples)。

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的 OData 集合筛选器进行故障排除](search-query-troubleshoot-collection-filters.md)
- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
