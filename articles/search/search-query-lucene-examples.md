---
title: Azure 搜索的 Lucene 查询示例 | Microsoft Docs
description: 模糊搜索、邻近搜索、术语提升、正则表达式搜索和通配符搜索的 Lucene 查询语法
author: LiamCa
manager: jlembicz
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: liamca
ms.openlocfilehash: 46e03834cb307ea103a8794616f6f38227881272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190084"
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>在 Azure 搜索中生成查询的 Lucene 查询语法示例
为 Azure 搜索构建查询时，可使用默认的[简单查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)或备用的 [Azure 搜索中的 Lucene 查询分析器](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)。 Lucene 查询分析程序支持更复杂的查询构造，如字段范围查询、模糊搜索、邻近搜索、字词提升和正则表达式搜索。

本文展示了使用完整语法时的查询操作示例，可以按照这些示例逐步操作。

## <a name="viewing-the-examples-in-jsfiddle"></a>在 JSFiddle 中查看示例

本文中的所有示例都是可执行查询，根可以对 [JSFiddle](https://jsfiddle.net)（用于测试脚本和 HTML 的联机代码编辑器）中的预加载搜索索引运行。 

若要运行示例，请右键单击查询示例 URL，在单独的浏览器窗口中打开 JSFiddle。

> [!NOTE]
> 下面的示例使用搜索索引，该索引包含基于由 [City of New York OpenData](https://nycopendata.socrata.com/) 计划提供的数据集得出的作业。 此数据不应认为是最新或完整数据。 索引位于 Microsoft 提供的沙盒服务上。 无需 Azure 订阅或 Azure 搜索即可试用这些查询。
>


## <a name="how-to-invoke-full-lucene-parsing"></a>如何调用完整 Lucene 分析

本文中的所有示例都指定了 queryType=full 搜索参数，指明由 Lucene 查询分析程序处理完整语法。 

**示例 1** -- 右键单击以下查询段，在加载 JSFiddle 和运行查询的新浏览器页面中打开。

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

在新的浏览器窗口中，JavaScript 源和 HTML 输出并排显示。 脚本引用完整查询（而不仅仅是代码段，如链接中所示）。 每个示例的 URL 中显示的都是完整查询。 

以下查询从“纽约市工作”索引（即在沙盒服务上加载的 nycjobs）返回文档。 为简洁起见，查询仅指定返回职位。 完整的基础查询如下所示：

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**searchFields** 参数将搜索限制在 business title 字段范围内。 “queryType”设为“完整”，这指示 Azure 搜索将 Lucene 查询分析器用于此查询。

> [!NOTE]
> 有关查询处理的背景信息，请参阅 [Azure 搜索中全文搜索的工作原理](search-lucene-query-architecture.md)。 有关搜索参数的详细信息，请参阅[搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。
>

### <a name="fielded-query-operation"></a>现场查询操作
可修改本文中的示例，方法是指定 **fieldname:searchterm** 构造来定义现场查询操作，该操作的字段是一个词，搜索词也是一个词或短语，并且根据需要使用布尔运算符。 一些示例包括以下内容：

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

如果想要两个字符串评估为单个实体，请务必将多个字符串放置在引号内，正如这个在位置字段中搜索两个不同城市的情况一样。 此外，请确保运算符大写，就像你看到的 NOT 和 AND 一样。

在 **fieldname:searchterm** 中指定的字段必须是可搜索字段。 有关如何在字段定义中使用索引属性的详细信息，请参阅[创建索引（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)。

**示例 2** -- 右键单击以下查询代码段。以下查询搜索包含“高级”（而非“初级”）一词的职位：

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>模糊搜索示例
模糊搜索在构造相似的术语中查找匹配项。 对于 [Lucene 文档](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)，模糊搜索基于 [Damerau-Levenshtein 距离](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance)。

若要执行模糊搜索，请在单个字词的末尾追加“~”波形符，后跟指定编辑距离的可选参数（介于 0 到 2 之间的值）。 例如“blue~”或“blue~1”返回 blue、blues 和 glue。

**示例 3** -- 右键单击以下查询段。 此查询搜索带有术语“关联”（拼写错误）的作业：

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> 不会[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)模糊查询，如果希望进行词干分解或词形还原，可能会感到惊讶。 只会对完整字词（字词查询或短语查询）进行词法分析。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对不完整查询字词执行的唯一转换操作是转换为小写。
>

## <a name="proximity-search-example"></a>邻近搜索示例
邻近搜索用于搜索文档中彼此邻近的术语。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。 例如“酒店机场”~5 将查找文档中彼此之间 5 个字以内的术语“酒店”和“机场”。

**示例 4** -- 右键单击查询。 搜索带有术语“高级分析师”的作业，分隔字数不超过一个字：

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**示例 5** -- 再次尝试删除术语“高级分析师”之间的词。

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>字词提升示例
术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 这不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。 以下示例有助于解释这些差异。

请考虑在某个字段中提升匹配项的计分配置文件，例如 musicstoreindex 示例中的“流派”。 术语提升可用于进一步提升高于其他术语的某些搜索词。 例如“rock^2 electronic”将提升在“流派”字段（高于搜索中的其他搜索字段）中包含搜索词的文档。 另外，由于术语提升值 (2) 的原因，包含搜索词“rock”的文档的排名要比包含搜索词“electronic”的要高。

若要提升术语，请使用插入符号“^”，并且所搜索术语末尾还要附加提升系数（数字）。 提升系数越高，术语相对于其他搜索词的相关性也越大。 默认情况下，提升系数是 1。 虽然提升系数必须是整数，但可以小于 1（例如 0.2）。

**示例 6** -- 右键单击查询。 搜索带有术语“计算机分析师”的作业时，我们看到没有结果同时包含“计算机”和“分析师”，但“分析师”作业排在结果顶部。

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**示例 7**  --  请重试，如果两个词都不存在，这一次提升带有术语“计算机”而非“分析师”的结果。

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>正则表达式示例
正则表达式搜索基于正斜杠“/”之间的内容查找匹配项，如在 [RegExp 类](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)中所记录的那样。

**示例 8** -- 右键单击查询。 搜索带有术语“高级”或“初级”的作业。

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

此示例的 URL 在页面中无法正常呈现。 解决方法是复制下面的 URL，并将其粘贴到浏览器 URL 地址：`http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>通配符搜索示例
可将通常可识别的语法用于多个 (\*) 或单个 (?) 字符通配符搜索。 请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

**示例 9** -- 右键单击查询。 搜索包含前缀“prog”的作业，这会包含带有术语“编程”和“程序员”的 business title。

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

不得将 * 或 ? 符号用作搜索的第一个字符。

## <a name="next-steps"></a>后续步骤
请尝试在代码中指定 Lucene 查询分析器。 以下链接介绍如何为 .NET 和 REST API 设置搜索查询。 链接使用默认的简单语法，因此需要应用从本文中所学知识指定 **queryType**。

* [使用 .NET SDK 查询 Azure 搜索索引](search-query-dotnet.md)
* [使用 REST API 查询 Azure 搜索索引](search-query-rest-api.md)

## <a name="see-also"></a>另请参阅

 [Azure 搜索中全文搜索的工作原理](search-lucene-query-architecture.md)