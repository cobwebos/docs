---
title: 知识探索服务 API 中的语义解释 | Microsoft Docs
description: 了解如何在认知服务中使用知识探索服务 (KES) API 中的语义解释。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 022188464eb7269b69f96a058b444167b587387c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365537"
---
# <a name="semantic-interpretation"></a>语义解释
语义解释通过语法将语义输出与每个解释路径相关联。  具体而言，服务计算解释遍历的 `tag` 元素中的语句序列，以计算最终输出。  

语句可能是文本分配或另一变量的变量。  此外，它还可向变量分配包含 0 或多个参数的函数的输出。  每个函数参数都可使用文本或变量指定。  如果函数不返回任何输出，则省略分配。

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

使用以字母开头，仅包含字母 (A-Z)、数字 (0-9) 和下划线 (\_) 的名称标识符指定变量。  从分配给它的文本或函数输出隐式推断出它的类型。 

以下是当前受支持的数据类型列表：

|Type|说明|示例|
|----|----|----|
|String|0 或更多字符的序列|"Hello World!"<br/>""|
|Bool|布尔值|是<br/>false|
|Int32|32 位带符号整数。  -2.1e9 到 2.1e9|123<br/>-321|
|Int64|64 位带符号整数。 -9.2e18 和 9.2e18|9876543210|
|Double|双精度浮点。 1.7e+/-308（15 位）|123.456789<br/>1.23456789e2|
|Guid|全局唯一标识符|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|查询|查询表达式，指示索引中的数据对象子集|All()<br/>And(q1, q2)|

<a name="semantic-functions"></a>
## <a name="semantic-functions"></a>语义函数
没有内置的语义函数集。  它们允许构造复杂查询，并提供对语法解释的上下文敏感控制。

### <a name="and-function"></a>And 函数
`query = And(query1, query2);`

返回由两个输入查询的交集组成的查询。

### <a name="or-function"></a>Or 函数
`query = Or(query1, query2);`

返回由两个输入查询的并集组成的查询。

### <a name="all-function"></a>All 函数
`query = All();`

返回包含所有数据对象的查询。

在以下示例中，我们以迭代方式使用 All() 函数根据 1 个或多个关键字的交集生成查询。

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>None 函数
`query = None();`

返回不包含任何数据对象的查询。

在以下示例中，我们以迭代方式使用 None() 函数根据 1 个或多个关键字的并集生成查询。

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Query 函数
```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

根据指定运算 op（默认值为“eq”），返回仅包含其属性 attrName 匹配值 value 的数据对象的查询。  通常情况下，使用 `attrref` 元素基于匹配的输入查询字符串创建查询。  如果给定值或通过其他方式获得值，可使用 Query() 函数创建匹配该值的查询。

在下面的示例中，使用 Query() 函数来实现对指定特定十年中的学术出版物的支持。

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

<a name="composite-function"/>
### <a name="composite-function"></a>Composite 函数
`query = Composite(innerQuery);`

返回一个查询，它封装一个由匹配组成的 innerQuery，该 innerQuery 针对常见复合属性 attr 的子属性。  封装要求任何匹配的数据对象的复合属性 attr 至少有一个单独满足 innerQuery 的值。  请注意，复合属性的子属性上的查询必须使用 Composite() 函数封装，然后才能与其他查询结合使用。

例如，以下查询将返回“harry shum”的学术出版物，同时他在“microsoft”：
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

另一方面，下面的查询返回学术出版物，其中一位作者是“harry shum”，且其中一个隶属关系是“microsoft”：
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable 函数
`value = GetVariable(name, scope);`

返回变量 name 的值，该值根据指定的作用域定义。  name 是以字母开头，仅包含字母 (A-Z)、数字 (0-9) 和下划线 (_) 的标识符。  作用域可设置为“请求”或“系统”。  请注意，根据不同作用域定义的变量有别于彼此，包括通过语义函数的输出定义的变量。

请求作用域变量在当前解释请求的所有解释之间共用。  可使用它们来控制通过语法搜索解释。

服务预定义系统变量并可用来检索有关系统当前状态的各种统计信息。  下面是当前支持的系统变量集：

|名称|Type|说明|
|----|----|----|
|IsAtEndOfQuery|Bool|如果当前解释匹配所有输入查询文本，则为 true|
|IsBeyondEndOfQuery|Bool|如果当前解释建议了输入查询文本之外的完成，则为 true|

### <a name="setvariable-function"></a>SetVariable 函数
`SetVariable(name, value, scope);`

根据指定的作用域，向变量 name 分配 value。  name 是以字母开头，仅包含字母 (A-Z)、数字 (0-9) 和下划线 (_) 的标识符。  目前 scope 唯一有效的值是“请求”。  没有任何可设置的系统变量。

请求作用域变量在当前解释请求的所有解释之间共用。  可使用它们来控制通过语法搜索解释。

### <a name="assertequals-function"></a>AssertEquals 函数
`AssertEquals(value1, value2);`

如果 value1 和 value2 等效，则函数成功，并且没有任何副作用。  否则，函数失败并拒绝解释。

### <a name="assertnotequals-function"></a>AssertNotEquals 函数
`AssertNotEquals(value1, value2);`

如果 value1 和 value2 不等效，则函数成功，并且没有任何副作用。  否则，函数失败并拒绝解释。


