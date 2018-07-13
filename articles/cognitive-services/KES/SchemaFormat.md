---
title: 知识探索服务 API 中的架构格式 | Microsoft Docs
description: 了解认知服务内知识探索服务 (KES) API 中的架构格式。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 3009392a5acb12a8f4df3d30a2cbe5e74f2172fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365509"
---
# <a name="schema-format"></a>架构格式
架构是在 JSON 文件中指定，此文件描述了用于创建索引的数据文件中对象的属性结构。  对于每个属性，架构都会指定名称、数据类型、可选操作和可选同义词列表。  对象的每个属性可能有 0 个或多个值。  下面展示了来自学术出版物领域的简化示例：

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

属性名是区分大小写的标识符，以字母开头，仅包含字母 (A-Z)、数字 (0-9) 和下划线 (\_)。  保留的“logprob”属性用于指定对象间的相对自然对数概率。

## <a name="attribute-type"></a>属性类型
下面列出了支持的属性数据类型：

| Type | 说明 | 操作 | 示例 |
|------|-------------|------------|---------|
| String | 字符串（1-1024 个字符） | equals、starts_with | "hello world" |
| Int32 | 已签名的 32 位整数 | equals、starts_with、is_between | 2016 |
| Int64 | 已签名的 64 位整数 | equals、starts_with、is_between | 9876543210 |
| Double | 双精度浮点值 | equals、starts_with、is_between | 1.602e-19 |
| 日期 | 日期（1400-01-01 到 9999-12-31） | equals、is_between | '2016-03-14' |
| Guid | 全局唯一标识符 | equals | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| Blob | 内部压缩的非索引数据 | 无 | "Empower every person and every organization on the planet to achieve more" |
| Composite | 多个子属性的复合| *不适用* | { "Name":"harry shum", "Affiliation":"microsoft" } |

String 属性用于表示可能在用户查询中显示的字符串值。  它们支持完全匹配的 equals 操作，以及适用于查询完成方案的 starts_with 操作（如匹配“micros”与“microsoft”）。  今后推出的版本将支持不区分大小写和模糊匹配，以便处理拼写错误。

Int32/Int64/Double 属性用于表示数值。  is_between 操作在运行时支持不等式（lt、le、gt、ge）。  starts_with 操作支持查询完成方案，如匹配“20”与“2016”或匹配“3.” 与“3.14”。

Date 属性用于对日期值进行高效编码。  is_between 操作在运行时支持不等式（lt、le、gt、ge）。
  
Guid 属性用于有效表示 GUID 值，默认支持 equals 操作。

Blob 属性用于对可能很大的数据 blob 进行高效编码，以便从相应对象执行运行时查找，而无需支持以 blob 值内容为依据的任何索引操作。

### <a name="composite-attributes"></a>复合属性
Composite 属性用于表示一组属性值。  每个子属性的名称以复合属性的名称开头，后跟“.”。  复合属性的值被指定为包含嵌套属性值的 JSON 对象。  复合属性可能有多个对象值。  不过，复合属性可能没有本身为复合属性的子属性。

在上述学术出版物示例中，借助复合属性，服务可以查询就职于“microsoft”的“harry shum”撰写的论文。  如果不使用复合属性，服务只能查询作者之一是“harry shum”且作者之一就职于“microsoft”的论文。  有关详细信息，请参阅[复合查询](SemanticInterpretation.md#composite-function)。

## <a name="attribute-operations"></a>属性操作
默认将每个属性都编入索引，以支持属性数据类型可用的所有操作。  如果不需要特定操作，可以明确指定索引操作集，从而减少索引大小。  在下面取自上述示例架构的代码片段中，已将 Author.Id 属性编入索引，以仅支持 equals 操作，而不支持适用于 Int32 属性的其他操作 starts_with 和 is_between。
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

在语法内引用属性时，需要在架构中指定 starts_with 操作，这样服务才能在部分查询的基础上完成查询。  

## <a name="attribute-synonyms"></a>属性同义词
可取的做法通常是，根据同义词引用特定字符串属性值。  例如，用户可能会将“Microsoft”引用为“MSFT”或“MS”。  在这种情况下，属性定义可以指定与此架构文件位于同一目录中的架构文件的名称。  同义词文件中的每一行都表示采用以下 JSON 格式的同义词条目：`["<canonical>", "<synonym>"]`。  在示例架构中，“AuthorName.syn”是包含 Author.Name 属性的同义词值的 JSON 文件。

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


规范值可能有多个同义词。  多个规范值可能共用一个同义词。  在这种情况下，服务通过多种解释来消除歧义。  下面展示了对应于上述架构的示例 AuthorName.syn 同义词文件：
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
