---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272734"
---
| 数据类型 | 带 `any` 的 lambda 表达式中允许的功能 | 带 `all` 的 lambda 表达式中允许的功能 |
|---|---|---|
| `Collection(Edm.ComplexType)` | 除 `search.ismatch` 和 `search.ismatchscoring` 外的所有内容 | 相同 |
| `Collection(Edm.String)` | 使用 `eq` 或 `search.in` 进行比较 <br/><br/> 使用 `or` 组合子表达式 | 使用 `ne` 或 `not search.in()` 进行比较 <br/><br/> 使用 `and` 组合子表达式 |
| `Collection(Edm.Boolean)` | 使用 `eq` 或 `ne` 进行比较 | 相同 |
| `Collection(Edm.GeographyPoint)` | 将 `geo.distance` 与 `lt` 或 `le` 配合使用 <br/><br/> `geo.intersects` <br/><br/> 使用 `or` 组合子表达式 | 将 `geo.distance` 与 `gt` 或 `ge` 配合使用 <br/><br/> `not geo.intersects(...)` <br/><br/> 使用 `and` 组合子表达式 |
| `Collection(Edm.DateTimeOffset)`、`Collection(Edm.Double)`、`Collection(Edm.Int32)`、`Collection(Edm.Int64)` | 使用 `eq`、`ne`、`lt`、`gt`、`le` 或 `ge` 进行比较 <br/><br/> 使用 `or` 将比较与其他子表达式组合 <br/><br/> 使用 `ne` 将除 `and` 以外的比较与其他子表达式组合 <br/><br/> 在`and`析取范式 (DNF)`or` 中使用 [ 和 ](https://en.wikipedia.org/wiki/Disjunctive_normal_form) 组合的表达式 | 使用 `eq`、`ne`、`lt`、`gt`、`le` 或 `ge` 进行比较 <br/><br/> 使用 `and` 将比较与其他子表达式组合 <br/><br/> 使用 `eq` 将除 `or` 以外的比较与其他子表达式组合 <br/><br/> 在`and`合取范式 (CNF)`or` 中使用 [ 和 ](https://en.wikipedia.org/wiki/Conjunctive_normal_form) 组合的表达式 |
