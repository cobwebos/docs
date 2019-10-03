---
title: 添加语言分析器 - Azure 搜索
description: Azure 搜索中适用于非英语查询和索引的多语言词法文本分析。
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
manager: nitinme
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
ms.openlocfilehash: e54fa449e0ed7f3208d9924b69946c6598a00444
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648812"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>向 Azure 搜索索引添加语言分析器

语言分析器是特定类型的[文本分析器](search-analyzers.md)，可以使用目标语言的语言规则执行词法分析。 每个可搜索字段都有一个“分析器”属性。 如果索引包含翻译的字符串，例如针对英文文本和中文文本的单独字段，则可在每个字段上指定语言分析器，以便访问这些分析器的丰富语言功能。  

Azure 搜索支持 35 个受 Lucene 支持的分析器，以及 Office 和必应中使用的专有 Microsoft 自然语言处理技术支持的 50 个分析器。

## <a name="comparing-analyzers"></a>比较分析器

某些开发人员可能首选更熟悉、简单的开源 Lucene 解决方案。 Lucene 语言分析器更快，但 Microsoft 分析器具有高级功能，如词形还原、字词分解（在德语、丹麦语、荷兰语、瑞典语、挪威语、爱沙尼亚语、芬兰语、匈牙利语、斯洛伐克语中）和实体识别（URL、电子邮件、日期、数字）。 如果可能，应对 Microsoft 和 Lucene 分析器进行比较以确定哪一个更合适。 

Microsoft 分析器的索引平均比 Lucene 的索引慢两到三倍，具体取决于语言。 对于平均大小的查询，搜索性能应该不会受到显著影响。 

### <a name="english-analyzers"></a>英语分析器

默认分析器为 Standard Lucene，它适用于英语，但可能不如 Lucene 的英语分析器或 Microsoft 的英语分析器那样适用。 
 
+ Lucene 的英语分析器扩展了标准分析器。 它从字词中删除所有格（尾部的 's）、根据 Porter 词干分解算法应用词干分解，并删除英语非索引字。  

+ Microsoft 的英语分析器执行词形还原，而不是词干分解。 这意味着它可以更好地处理曲折和不规则字词形式，从而产生相关度更高的搜索结果。 

## <a name="configuring-analyzers"></a>配置分析器

语言分析器按原样使用。 对于索引定义中的每个字段，可将分析器属性设置为用于指定语言和语言学堆栈（Microsoft 或 Lucene）的分析器名称。 将在为该字段编入索引和搜索时应用相同的分析器。 例如，可以为在同一个索引中并行存在的英语、法语和西班牙语酒店说明使用单独的字段。 或者，可以使用 indexAnalyzer 和 searchAnalyzer 代替“分析器”，以在索引或查询时具有不同的分析规则。 

使用 **searchFields** 查询参数指定在查询中针对哪个特定于语言的字段进行搜索。 可在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)中查看包含分析器属性的查询示例。 

有关索引属性的详细信息，请参阅[创建索引（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)。 若要详细了解 Azure 搜索中的分析，请参阅 [Azure 搜索中的分析器](https://docs.microsoft.com/azure/search/search-analyzers)。

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>语言分析器列表 
 下面是受支持语言的列表以及 Lucene 和 Microsoft 分析器名称。  

|语言|Microsoft 分析器名称|Lucene 分析器名称|  
|--------------|-----------------------------|--------------------------|  
|阿拉伯语|ar.microsoft|ar.lucene|  
|亚美尼亚语||hy.lucene|  
|孟加拉语|bn.microsoft||  
|巴斯克语||eu.lucene|  
|保加利亚语|bg.microsoft|bg.lucene|  
|加泰罗尼亚语|ca.microsoft|ca.lucene|  
|中文(简体)|zh-Hans.microsoft|zh-Hans.lucene|  
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
|印地语|hi.microsoft|hi.lucene|  
|匈牙利语|hu.microsoft|hu.lucene|  
|冰岛语|is.microsoft||  
|印度尼西亚语|id.microsoft|id.lucene|  
|爱尔兰语||ga.lucene|  
|意大利语|it.microsoft|it.lucene|  
|日语|ja.microsoft|ja.lucene|  
|卡纳达语|kn.microsoft||  
|韩语|ko.microsoft|ko.lucene|  
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
|塞尔维亚语(西里尔文)|sr-cyrillic.microsoft||  
|塞尔维亚语(拉丁语系)|sr-latin.microsoft||  
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
 [创建索引（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [AnalyzerName Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)（AnalyzerName 类）  
 [Video: module 7 of Azure Search MVA presentation](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07)（视频：Azure 搜索 MVA 演示文稿的模块 7）。  

