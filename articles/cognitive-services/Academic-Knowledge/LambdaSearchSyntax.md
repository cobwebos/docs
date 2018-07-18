---
title: 学术知识 API 中的 Lambda 搜索语法 | Microsoft Docs
description: 了解可以在 Microsoft 认知服务的学术知识 API 中使用的 Lambda 搜索语法。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f486368e1d0258087091acb846a84b125712db40
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365427"
---
# <a name="lambda-search-syntax"></a>Lambda 搜索语法

每个 lambda 搜索查询字符串均说明一个关系图模式。 查询必须具有至少一个开始节点，指定我们开始进行遍历所在的关系图节点。 若要指定开始节点，请调用 MAG.StartFrom() 方法并传入一个或多个节点的 ID 或指定搜索约束的查询对象。 StartFrom() 方法具有三个重载。 所有这些重载均采用两个参数，其中第二个参数为可选参数。 第一个参数可以是长整型、长整型的可枚举集合或表示 JSON 对象的字符串（与 json 搜索中的语义相同）：
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

编写 lambda 搜索查询的过程是从一个节点遍历到另一个节点。 若要指定要遍历的边缘类型，请使用 FollowEdge() 并传入所需的边缘类型。 FollowEdge() 使用字符串参数的任意数：
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> 如果我们不在意要遵循的边缘类型，只需省略两个节点之间的 FollowEdge()：查询将遍历这两个节点之间的所有可能的边缘。

我们可以通过 VisitNode() 指定要在节点上采取的遍历操作，即，是在此节点中停止并返回当前路径作为结果，还是继续浏览关系图。  枚举类型 Action定义两种类型的操作：Action.Return 和 Action.Continue。 我们可以将此类枚举值直接传递给 VisitNode()，或将其与按位 AND 运算符“&”组合。 将这两种操作组合在一起意味着将执行这两种操作。 注意：不要对操作使用按位 OR 运算符“|”。 使用此运算符将导致查询中止，且不会返回任何结果。 跳过两个 FollowEdge() 调用之间的 VisitNode() 将导致查询在到达节点后无条件浏览关系图。

```
VisitNode(Action action, IEnumerable<string> select = null)
```

对于 VisitNode()，我们也可以传入类型为 Expression\<Func\<INode, Action\>\> 的 lambda 表达式，它采用 INode 并返回遍历操作：

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>INode 

INode 提供只读数据访问接口和节点上的少数内置帮助程序函数。 

### <a name="basic-data-access-interfaces"></a>基本数据访问接口

##### <a name="long-cellid"></a>长 CellID

节点的 64 位 ID。 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

获取指定属性的值。 T 是必需类型，字段将解释为该类型。 如果必需类型无法从字段类型中隐式转换，则将尝试自动类型强制转换。 注意：当属性具有多个值时，GetField\<string\> 将导致列表被序列化为一个 Json 字符串 ["val1", "val2", ...]。如果属性不存在，它将引发异常，并中止当前关系图浏览。

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

指示在当前节点中是否存在含有给定名称的字段。

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

工作方式类似于 GetField\<string\>(fieldName)。 但是，当未找到字段时，它不会引发异常，而是改为返回空字符串 ("")。

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

指示在当前节点中是否存在给定属性。 与 ContainsField(fieldName) 相同。

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

指示属性是否具有给定值。 

##### <a name="int-countstring-fieldname"></a>int count(string fieldname)

获取给定属性的值的计数。 当属性不存在时，返回 0。

### <a name="built-in-helper-functions"></a>内置帮助程序函数

##### <a name="action-returnifbool-condition"></a>Action return_if(bool condition)

如果条件为 true，则返回 Action.Return。 如果条件为 false 且此表达式未使用按位 AND 运算符与其他操作联接，则将中止关系图浏览。

##### <a name="action-continueifbool-condition"></a>Action continue_if(bool condition)

如果条件为 true，则返回 Action.Continue。 如果条件为 false 且此表达式未使用按位 AND 运算符与其他操作联接，则将中止关系图浏览。

##### <a name="bool-dicedouble-p"></a>bool dice(double p)

生成大于或等于 0.0 并小于 1.0 的随机数字。 此函数仅在该数字小于或等于 p 时才返回 true。

与 json 搜索相比，lambda 搜索表达得更为清楚：C# lambda 表达式可直接用于指定查询模式。 下面是两个示例。

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
