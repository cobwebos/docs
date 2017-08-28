---
title: "OMS Log Analytics 日志搜索中的正则表达式 | Microsoft 文档"
description: "可以在 Log Analytics 日志搜索中使用 RegEx 关键字，根据正则表达式筛选结果。  本文为这些表达式的语法提供了多个示例。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 9746170f157ed5065adc953a31687ff18bd73708
ms.contentlocale: zh-cn
ms.lasthandoff: 08/11/2017

---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>在 Log Analytics 中使用正则表达式筛选日志搜索结果

可以通过[日志搜索](log-analytics-log-searches.md)从 Log Analytics 存储库提取信息。  可以通过[筛选表达式](log-analytics-search-reference.md#filter-expressions)按特定条件筛选搜索结果。  可以使用 **RegEx** 关键字为该筛选器指定正则表达式。  

本文详细介绍了 Log Analytics 使用的正则表达式语法。

> [!NOTE]
> 你只能使用带可搜索字段的正则表达式。  有关可搜索字段的详细信息，请参阅[在 Log Analytics 中使用日志搜索查找数据](log-analytics-log-searches.md#use-additional-filters)中的“字段类型”。


## <a name="regex-keyword"></a>RegEx 关键字

请通过以下语法在日志搜索中使用 **RegEx** 关键字。  若要确定正则表达式本身的语法，可参阅本文其他部分。

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

例如，若要通过正则表达式返回类型为*警告* 或*错误* 的警报记录，可使用以下日志搜索。

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>部分匹配
请注意，正则表达式必须与属性的整个文本匹配。  部分匹配不会返回任何记录。  例如，如果尝试从名为 srv01.contoso.com 的计算机返回记录，则以下日志搜索**不会**返回任何记录。

    Computer=RegEx("srv..")

这是因为仅名称的第一部分与正则表达式匹配。  下面的两个日志搜索会从该计算机返回记录，因为与整个名称匹配。

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>字符
指定不同的字符。

| Character | 说明 | 示例 | 示例匹配 |
|:--|:--|:--|:--|
| a | 字符出现一次。 | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| 。 | 任何单个字符。 | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | 字符出现零次或一次。 | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | 字符出现零次或多次。 | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | 字符出现一次或多次。 | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | 与方括号中的任何单个字符匹配。 | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | 与范围中的单个字符匹配。  可以包含多个范围。 | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | 不是方括号中的任何字符。 | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | 不是范围中的任何字符。 | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [n-m] | 与数字字符范围匹配。 | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | 任何字符串。 | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>字符出现多次
指定特定字符出现多次。

| Character | 说明 | 示例 | 示例匹配 |
|:--|:--|:--|:--|
| a{n} |  字符出现 n 次。 | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  字符至少出现 n 次。 | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  字符出现 n 到 m 次。 | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>逻辑表达式
从多个值中进行选择。

| Character | 说明 | 示例 | 示例匹配 |
|:--|:--|:--|:--|
| &#124; | 逻辑 OR。  如果任一表达式匹配，则返回结果。 | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | 警告<br>错误 |
| & | 逻辑 AND。  如果两个表达式都匹配，则返回结果 | EventData=regex("(Security.\*&.\*success.\*)") | Security auditing successful |


## <a name="literals"></a>文本
将特殊字符转换为文本字符。  这包括为正则表达式提供功能的字符，例如 ?-\*^\[\]{}\(\)+\|.&。

| Character | 说明 | 示例 | 示例匹配 |
|:--|:--|:--|:--|
| \\ | 将特殊字符转换为文本。 | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Error]File not found.<br>Error-File not found. |


## <a name="next-steps"></a>后续步骤

* 熟悉[日志搜索](log-analytics-log-searches.md)，以便查看和分析 Log Analytics 存储库中的数据。

