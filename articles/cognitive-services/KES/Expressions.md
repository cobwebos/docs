---
title: 知识探索服务 API 中的结构化查询表达式 | Microsoft Docs
description: 了解如何使用认知服务的知识探索服务 (KES) API 中的结构化查询表达式。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 070ee311a1153bc9fb59870dce68f385a43b15f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365504"
---
# <a name="structured-query-expression"></a>结构化查询表达式
结构化查询表达式指定了一组要根据数据索引进行评估的操作。  它包含属性查询表达式和更高级别函数。  [evaluate](evaluateMethod.md) 方法可用于计算与表达式匹配的对象。  下面的示例来自学术出版物领域，它返回自 2013 年起由 Jaime Teevan 撰写的出版物。

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

可通过 [interpret](interpretMethod.md) 请求获取结构化查询表达式，其中每个解释的语义输出都是结构化查询表达式，用于返回与输入自然语言查询匹配的索引对象。  也可以使用本部分中的语法手动编写。

## <a name="attribute-query-expression"></a>属性查询表达式
属性查询表达式确定了一组与特定属性匹配的对象。  支持的匹配操作因[架构](SchemaFormat.md)中指定的属性类型和索引操作而异：

| Type | Operation | 示例 |
|------|-------------|------------|
| String | equals | Title='latent semantic analysis'（规范 + 同义词） |
| String | equals | Author.Name=='susan t dumais'（仅规范）|
| String | starts_with | Title='latent s'... |
| Int32/Int64/Double | equals | Year=2000 |
| Int32/Int64/Double | starts_with | Year='20'...（任何以“20”开头的十进制值） |
| Int32/Int64/Double | is_between | Year&lt;2000 <br/> Year&lt;=2000 <br/> Year&gt;2000 <br/> Year&gt;=2000 <br/> Year=[2010,2012]（仅包括左边界值：2010、2011） <br/> Year=[2000,2012]（包括两个边界值：2010、2011、2012） |
| 日期 | equals | BirthDate='1984-05-14' |
| 日期 | is_between | BirthDate&lt;='2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | equals | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


例如，表达式“Title='latent s'...”匹配标题以字符串“latent s”开头的所有学术出版物。  属性 Title 必须在用于生成索引的架构中指定“starts_with”操作，才能评估此表达式。

对于关联有同义词的属性，查询表达式可以使用“==”运算符指定规范值与给定字符串匹配的对象，也可以使用“=”运算符指定任意规范值/同义词值匹配的对象。  两者都需要在属性定义中指定“equals”运算符。


## <a name="functions"></a>函数
内置有一组函数，可便于通过基本属性查询构造更复杂的查询表达式。

### <a name="and-function"></a>And 函数
`And(expr1, expr2)`

返回两个输入查询表达式的交集。

下面的示例返回 2000 年发表的与信息检索相关的学术出版物：

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Or 函数
`Or(expr1, expr2)`

返回两个输入查询表达式的并集。

下面的示例返回 2000 年发表的与信息检索或用户建模相关的学术出版物：

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Composite 函数
`Composite(expr)`

返回封装内部表达式的表达式，内部表达式包含对常见复合属性的子属性执行的查询。  封装要求，任何匹配数据对象的复合属性至少必须有一个值单独满足内部表达式。  请注意，必须使用 Composite() 函数封装对复合属性的子属性执行的查询表达式，然后才能将它与其他查询表达式结合使用。

例如，下面的表达式返回就职于“microsoft”的“harry shum”的学术出版物：

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

相比之下，下面的表达式返回作者之一是“harry shum”且附属关系之一是“microsoft”的学术出版物：

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
