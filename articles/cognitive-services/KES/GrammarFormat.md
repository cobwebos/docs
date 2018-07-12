---
title: 知识探索服务 API 中的语法格式 | Microsoft Docs
description: 了解认知服务的 知识探索服务 (KES) API 中的语法格式。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 27202379b8c36696a380049336229cac040b0108
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365561"
---
# <a name="grammar-format"></a>语法格式
语法是一个 XML 文件，它指定服务可解释的一组加权的自然语言查询，并指定了如何将这些自然语言查询转换为语义查询表达式。  语法句法基于 [SRGS](http://www.w3.org/TR/speech-grammar/)，这是一个面向语音识别语法的 W3C 标准，具有支持数据索引集成和语义函数的扩展程序。

下面描述了可在语法中使用的每个句法元素。  要了解在上下文中展示这些元素用法的完整语法，请参阅[本例](#example)。

### <a name="grammar-element"></a>grammar 元素 
`grammar` 元素是语法规范 XML 中的顶级元素。  `root` 属性是必需的，它指定了根规则的名称，而根规则定义了语法的起始点。

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>import 元素
`import` 元素导入外部文件中的架构定义来实现属性引用。 此元素必须是顶级 `grammar` 元素的子元素，且显示在所有 `attrref` 元素的前面。 `schema` 属性是必需的，它指定了与语法 XML 文件位于同一目录的架构文件的名称。 `name` 元素也是必需的，它指定了后续 `attrref` 元素在引用此架构中定义的属性时使用的架构别名。

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>rule 元素
`rule` 元素定义了语法规则，该规则是一个结构单元，用于指定系统可解释的一组查询表达式。  此元素必须是顶级 `grammar` 元素的子元素。  `id` 属性是必需的，它指定从 `grammar` 或 `ruleref` 元素中引用的规则的名称。

`rule` 元素定义了一组合法扩展。  文本令牌直接针对数据查询进行匹配。  `item` 元素指定了重复项并更改解释概率。  `one-of` 元素指示替代选项。  `ruleref` 元素可用于根据简单的扩展构造更复杂的扩展。  `attrref` 元素允许针对索引的属性值进行匹配。  `tag` 元素指定解释的语义，且可更改解释概率。

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>example 元素
`example` 元素可选，它指定所含 `rule` 定义可能接受的示例短语。  此元素可用于文档和自动测试。

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>item 元素
`item` 元素将一系列语法构造进行分组。  它可用于指示扩展序列的重复，或结合 `one-of` 元素来指定替代项。

当 `item` 元素不是 `one-of` 元素的子级时，它可向计数值分配 `repeat` 属性，从而指定封闭序列的重复。  计数值“n”（其中 n 是一个整数）指示序列必须恰好发生 n 次。  计数值“m-n”允许序列出现 m 到 n 次（包含 m 次和 n 次）。  计数值“m-”指定序列必须至少出现 m 次。  `repeat-logprob` 属性可选，它可用于更改超过最小值的每个额外重复的解释概率。

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

当 `item` 元素显示为 `one-of` 元素的子级时，它定义一组扩展替代项。  在此用法中，可选的 `logprob` 属性指定不同选项之间的相对对数概率。  如果概率 p 介于 0 到 1 之间，则相应的对数概率可计算为 log(*p*)，其中 log() 是自然对数函数。  若未指定，则 `logprob` 默认为 0，即不更改解释概率。  请注意，对数概率始终为负的浮点值或 0。

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>one-of 元素
`one-of` 元素指定其中一个子级 `item` 元素中的备选扩展。  `one-of` 元素中仅可出现 `item` 元素。  不同选项间的相对概率可通过每个子级 `item` 中的 `logprob` 进行指定。

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref 元素
`ruleref` 元素通过引用其他 `rule` 元素指定有效扩展。  通过使用 `ruleref` 元素，可基于更简单的规则构建更复杂的表达式。  `uri` 属性是必需的，它使用语法“#*rulename*”指定所引用的 `rule` 的名称。  要捕获所引用语法的语义输出，需使用可选的 `name` 属性来指定要将语义输出分配到的变量的名称。
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref 元素
`attrref` 元素引用索引属性，从而能够针对索引中观察到的属性值进行匹配。  `uri` 属性是必需的，它使用“*schemaName*#*attrName*”指定索引架构名称和属性名称。  必须存在前置 `import` 元素，它用于导入名为 schemaName 的架构。  属性名称是相应架构中定义的属性的名称。

除了匹配用户输入，`attrref` 元素还返回一个结构化的查询对象作为输出，该对象选择匹配输入值的索引中的对象子集。  可选的 `name` 属性可用于指定应存储查询对象输出的变量的名称。  该查询对象可包含其他查询对象，构成更复杂的表达式。  有关详细信息，请参阅[语义解释](SemanticInterpretation.md)。  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>查询完成 
要在解释部分用户查询时支持“查询完成”功能，所引用的每个属性必须在架构定义中包含“starts_with”作为操作。  如果具有用户查询前缀，则 `attrref` 将匹配索引中补全前缀的所有值，并生成每个完整的值作为语法的单独解释。  

示例：
* 如果针对查询前缀“dat”匹配 `<attrref uri="academic#Keyword" name="keyword"/>`，则对“数据库”论文生成一个解释，对“数据挖掘”论文再生成一个解释等等。
* 如果针对查询前缀“200”匹配 `<attrref uri="academic#Year" name="year"/>`，则对“2000”年的论文生成一个解释，对“2001”年的论文再生成一个解释等等。

#### <a name="matching-operations"></a>匹配操作
除了完全匹配，select 属性类型还通过可选的 `op` 属性支持前缀和不等性匹配。  如果索引中的对象均不具有匹配值，则语法路径受阻，且服务不生成任何遍历此语法路径的解释。   `op` 属性默认为“eq”。

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

下表列出了每个属性类型支持的 `op` 值。  要使用这些值，架构属性定义中需要包含相应的索引操作。

| 属性类型 | Op 值 | 说明 | 索引操作
|----|----|----|----|
| String | eq | 字符串完全匹配 | equals |
| String | starts_with | 字符串前缀匹配 | starts_with |
| Int32、Int64、Double | eq |  数字相等性匹配 | equals |
| Int32、Int64、Double | lt、le、gt、ge | 数字不等性匹配（<、<=、>、>=） | is_between |
| Int32、Int64、Double | starts_with | 十进制表示法中值的前缀匹配 | starts_with |

示例：
* `<attrref uri="academic#Year" op="lt" name="year"/>` 与输入字符串“2000”相匹配，并返回 2000 年之前（包含 2000 年）发布的所有论文。
* `<attrref uri="academic#Year" op="lt" name="year"/>` 与输入字符串“20”不匹配，原因是在索引中，20 年之前未发布任何论文。
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` 与输入字符串“dat”相匹配，并在单个解释中返回“数据库”和“数据挖掘”等论文。这种用法很少见。
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` 与输入字符串“20”相匹配，并在单个解释中返回 200-299 和 2000-2999 年发布的论文。这种用法很少见。

### <a name="tag-element"></a>tag 元素
`tag` 元素指定要如何解释贯穿语法的路径。  它包含一系列以分号结束的语句。  语句可能是文本分配，也可能是另一变量的变量。  此外，它还可向变量分配不带参数或带参数的函数的输出。  每个函数参数都可使用文本或变量指定。  如果函数不返回任何输出，则略掉分配。  变量范围是“包含”规则的本地范围。

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

语法中的每个 `rule` 均具有名为“out”的预定义变量，它表示此规则的语义输出。  其值按如下方式计算：通过匹配用户查询输入的 `rule` 评估路径遍历的每个语义语句。  在评估结束时分配给“out”变量的值就是规则的语义输出。  针对语法解释用户查询的语义输出就是根规则的语义输出。

一些语句可能引入加法对数概率偏移量，从而使解释路径的概率出现变化。  如果不满足指定的条件，某些语句可能完全拒绝解释。

如需所支持的语义函数的列表，请参阅[语义函数](SemanticInterpretation.md#semantic-functions)。

## <a name="interpretation-probability"></a>解释概率
贯穿语法的解释路径的概率是指整个路径中遇到的所有 `<item>` 元素和语义函数的累计对数概率。  它描述了与特定输入序列相匹配的相对可能性。

如果概率 p 介于 0 到 1 之间，则相应的对数概率可计算为 log(*p*)，其中 log() 是自然对数函数。  通过使用对数概率，系统可累计贯穿简单加法的解释路径的联合概率。  它还能避免此类联合概率计算中常见的浮点数下溢。  请注意，根据设计，对数概率始终为负浮点值或 0，其中值越大表示可能性越大。

<a name="example"></a>
## <a name="example"></a>示例
下面是学术刊物域中的一个示例 XML，它展示了语法的各个元素：

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
