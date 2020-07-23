---
title: 向字符串字段添加语言分析器
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索中的非英语查询和索引的多语言词法分析。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 8f0909ee1cdce1e6180b91a30b2e9b281098c826
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130545"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>向 Azure 认知搜索索引中的字符串字段添加语言分析器

语言分析器是特定类型的[文本分析器](search-analyzers.md)，可以使用目标语言的语言规则执行词法分析。 每个可搜索字段都有一个“分析器”属性  。 如果内容包含翻译后的字符串（例如英语和中文文本的单独字段），则可以在每个字段上指定语言分析器以访问这些分析器的丰富语言功能。

## <a name="when-to-use-a-language-analyzer"></a>何时使用语言分析器

当了解 word 或句子结构时，应考虑使用语言分析器将值添加到文本分析。 常见的例子是，不规则谓词形式（"携带" 和 "带"）或复数名词（"鼠标" 和 "鼠标"）的关联。 如果没有语言感知，这些字符串将单独在物理特征上分析，这将无法捕获连接。 由于大块文本更有可能包含此内容，因此，由说明、评论或摘要组成的字段非常适合用于语言分析器。

当内容由非西方语言字符串组成时，还应考虑语言分析器。 尽管[默认分析器](search-analyzers.md#default-analyzer)是与语言无关的，但使用空格和特殊字符（连字符和斜杠）来分隔字符串的概念往往比非西方语言更适用于西方语言。 

例如，在中文、日语、韩语（CJK）和其他亚洲语言中，空格不一定是单词分隔符。 请看下面的日语字符串。 由于它没有空格，因此与语言无关的分析器可能会将整个字符串分析为一个标记，实际上该字符串实际上是一个短语。

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

对于上面的示例，成功查询必须包含完整的令牌或使用后缀通配符的部分令牌，导致非自然并限制搜索体验。

更好的体验是搜索各个词：明るい（鲜）、私たちの（我们的）、銀河系（Galaxy）。 使用认知搜索中提供的其中一个日语分析器更有可能会解锁此行为，因为这些分析器更适合将文本块拆分为目标语言中有意义的单词。

## <a name="comparing-lucene-and-microsoft-analyzers"></a>比较 Lucene 和 Microsoft 分析器

Azure 认知搜索支持由 Lucene 支持的35语言分析器，以及由 Office 和必应使用的专有 Microsoft 自然语言处理技术支持的50语言分析器。

某些开发人员可能首选更熟悉、简单的开源 Lucene 解决方案。 Lucene 语言分析器更快，但 Microsoft 分析器具有高级功能，如词形还原、字词分解（在德语、丹麦语、荷兰语、瑞典语、挪威语、爱沙尼亚语、芬兰语、匈牙利语、斯洛伐克语中）和实体识别（URL、电子邮件、日期、数字）。 如果可能，应对 Microsoft 和 Lucene 分析器进行比较以确定哪一个更合适。 

Microsoft 分析器的索引平均比 Lucene 的索引慢两到三倍，具体取决于语言。 对于平均大小的查询，搜索性能应该不会受到显著影响。 

### <a name="english-analyzers"></a>英语分析器

默认分析器为 Standard Lucene，它适用于英语，但可能不如 Lucene 的英语分析器或 Microsoft 的英语分析器那样适用。 
 
+ Lucene 的英语分析器扩展了标准分析器。 它从字词中删除所有格（尾部的 's）、根据 Porter 词干分解算法应用词干分解，并删除英语非索引字。  

+ Microsoft 的英语分析器执行词形还原，而不是词干分解。 这意味着它可以更好地处理变形和不规则的单词窗体，从而导致更相关的搜索结果 

## <a name="configuring-analyzers"></a>配置分析器

语言分析器按原样使用。 对于索引定义中的每个字段，可将分析器属性设置为用于指定语言和语言学堆栈（Microsoft 或 Lucene）的分析器名称****。 将在为该字段编入索引和搜索时应用相同的分析器。 例如，可以为在同一个索引中并行存在的英语、法语和西班牙语酒店说明使用单独的字段。

> [!NOTE]
> 不能在为字段编制索引时和查询时使用不同的语言分析器。 该功能是为[自定义分析器](index-add-custom-analyzers.md)保留的。 因此，如果尝试将 **searchAnalyzer** 或 **indexAnalyzer** 属性设为语言分析器的名称，REST API 将返回错误响应。 必须改用 **analyzer** 属性。

使用 **searchFields** 查询参数指定在查询中针对哪个特定于语言的字段进行搜索。 您可以查看在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)中包含 analyzer 属性的查询示例。 

有关索引属性的详细信息，请参阅[创建索引（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)。 若要详细了解 Azure 认知搜索中的分析，请参阅 [Azure 认知搜索中的分析器](https://docs.microsoft.com/azure/search/search-analyzers)。

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>语言分析器列表 
 下面是受支持语言的列表以及 Lucene 和 Microsoft 分析器名称。  

|语言|Microsoft 分析器名称|Lucene 分析器名称|  
|--------------|-----------------------------|--------------------------|  
|阿拉伯语|ar.microsoft|ar.lucene|  
|亚美尼亚语||hy.lucene|  
|Bangla|bn.microsoft||  
|巴斯克语||eu.lucene|  
|保加利亚语|bg.microsoft|bg.lucene|  
|加泰罗尼亚语|ca.microsoft|ca.lucene|  
|简体中文|zh-Hans.microsoft|zh-Hans.lucene|  
|中文(繁体)|zh-Hant.microsoft|zh-Hant.lucene|  
|克罗地亚语|hr.microsoft||  
|捷克语|cs.microsoft|cs.lucene|  
|丹麦语|da.microsoft|da.lucene|  
|荷兰语|nl.microsoft|nl.lucene|  
|英语|en.microsoft|en.lucene|  
|爱沙尼亚语|et.microsoft||  
|芬兰语|fi.microsoft|fi.lucene|  
|法语|fr.microsoft|fr.lucene|  
|加利西亚语||gl.lucene|  
|德语|de.microsoft|de.lucene|  
|希腊语|el.microsoft|el.lucene|  
|古吉拉特语|gu.microsoft||  
|希伯来语|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|匈牙利语|hu.microsoft|hu.lucene|  
|冰岛语|is.microsoft||  
|印度尼西亚语|id.microsoft|id.lucene|  
|爱尔兰语||ga.lucene|  
|意大利语|it.microsoft|it.lucene|  
|日语|ja.microsoft|ja.lucene|  
|卡纳达语|kn.microsoft||  
|朝鲜语|ko.microsoft|ko.lucene|  
|拉脱维亚语|lv.microsoft|lv.lucene|  
|立陶宛语|lt.microsoft||  
|马拉雅拉姆语|ml.microsoft||  
|马来语(拉丁语系)|ms.microsoft||  
|马拉地语|mr.microsoft||  
|挪威语|nb.microsoft|no.lucene|  
|波斯语||fa.lucene|  
|波兰语|pl.microsoft|pl.lucene|  
|葡萄牙语(巴西)|pt-Br.microsoft|pt-Br.lucene|  
|葡萄牙语(葡萄牙)|pt-Pt.microsoft|pt-Pt.lucene|  
|旁遮普语|pa.microsoft||  
|罗马尼亚语|ro.microsoft|ro.lucene|  
|俄语|ru.microsoft|ru.lucene|  
|塞尔维亚语（西里尔）|sr-cyrillic.microsoft||  
|塞尔维亚语（拉丁）|sr-latin.microsoft||  
|斯洛伐克语|sk.microsoft||  
|斯洛文尼亚语|sl.microsoft||  
|西班牙语|es.microsoft|es.lucene|  
|瑞典语|sv.microsoft|sv.lucene|  
|泰米尔语|ta.microsoft||  
|泰卢固语|te.microsoft||  
|泰语|th.microsoft|th.lucene|  
|土耳其语|tr.microsoft|tr.lucene|  
|乌克兰语|uk.microsoft||  
|乌尔都语|ur.microsoft||  
|越南语|vi.microsoft||  

 名称带有 **Lucene** 批注的所有分析器都由 [Apache Lucene 的语言分析器](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )提供支持。

## <a name="see-also"></a>请参阅  

+ [创建索引（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)（AnalyzerName 类）  

