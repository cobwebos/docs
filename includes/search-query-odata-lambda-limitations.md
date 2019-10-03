---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079634"
---
| 数据类型 | 使用 lambda 表达式中允许的功能 `any` | 使用 lambda 表达式中允许的功能 `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | 以外的所有内容`search.ismatch`和 `search.ismatchscoring` | 相同 |
| `Collection(Edm.String)` | 具有比较`eq`或 `search.in` <br/><br/> 结合使用的子表达式 `or` | 具有比较`ne`或 `not search.in()` <br/><br/> 结合使用的子表达式 `and` |
| `Collection(Edm.Boolean)` | 具有比较`eq`或 `ne` | 相同 |
| `Collection(Edm.GeographyPoint)` | 使用`geo.distance`与`lt`或 `le` <br/><br/> `geo.intersects` <br/><br/> 结合使用的子表达式 `or` | 使用`geo.distance`与`gt`或 `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> 结合使用的子表达式 `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | 使用的比较`eq`， `ne`， `lt`， `gt`， `le`，或 `ge` <br/><br/> 将与使用其他子表达式结合使用的比较 `or` <br/><br/> 组合比较除`ne`与使用其他子表达式 `and` <br/><br/> 表达式使用的组合`and`并`or`中[析取范式 (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | 使用的比较`eq`， `ne`， `lt`， `gt`， `le`，或 `ge` <br/><br/> 将与使用其他子表达式结合使用的比较 `and` <br/><br/> 组合比较除`eq`与使用其他子表达式 `or` <br/><br/> 表达式使用的组合`and`并`or`中[合正常窗体 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
