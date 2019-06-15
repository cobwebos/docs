---
title: 在 Azure Monitor 日志查询中使用字符串 | Microsoft Docs
description: 介绍如何在 Azure Monitor 日志查询中编辑、比较、搜索字符串以及对其执行其他各种操作。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 4b2763629a3036551cb3d362e609c72737436f4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424697"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>在 Azure Monitor 日志查询中使用字符串


> [!NOTE]
> 应完成[开始使用 Azure 监视器 Log Analytics](get-started-portal.md)并[开始使用 Azure Monitor 日志查询](get-started-queries.md)之前完成本教程。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

本文介绍如何编辑、比较、搜索字符串以及对其执行其他各种操作。

字符串中的每个字符都有一个与其位置相符的索引号。 第一个字符位于索引 0 处，下一个字符位于索引 1 处，依此类推。 不同的字符串函数使用以下各部分所示的索引号。 下面的许多示例使用 **print** 命令来演示在不使用特定数据源的情况下如何处理字符串。


## <a name="strings-and-escaping-them"></a>字符串及其转义
字符串值包装在单引号或双引号字符中。 反斜杠 (\) 用于将字符转义为其后面的字符，例如，\t 表示 tab（制表符），\n 表示 newline（换行符），\" 表示引号字符本身。

```Kusto
print "this is a 'string' literal in double \" quotes"
```

为了防止“\\”用作转义字符，请添加“\@”作为字符串的前缀：

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>字符串比较

运算符       |描述                         |区分大小写|示例（生成 `true`）
---------------|------------------------------------|--------------|-----------------------
`==`           |等于                              |是           |`"aBc" == "aBc"`
`!=`           |不等于                          |是           |`"abc" != "ABC"`
`=~`           |等于                              |否            |`"abc" =~ "ABC"`
`!~`           |不等于                          |否            |`"aBc" !~ "xyz"`
`has`          |右侧是左侧的整个字词 |否|`"North America" has "america"`
`!has`         |右侧不是左侧的完整字词       |否            |`"North America" !has "amer"` 
`has_cs`       |右侧是左侧的整个字词 |是|`"North America" has_cs "America"`
`!has_cs`      |右侧不是左侧的完整字词       |是            |`"North America" !has_cs "amer"` 
`hasprefix`    |右侧是左侧的字词前缀         |否            |`"North America" hasprefix "ame"`
`!hasprefix`   |右侧不是左侧的字词前缀     |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右侧是左侧的字词前缀         |是            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右侧不是左侧的字词前缀     |是            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右侧是左侧的字词后缀         |否            |`"North America" hassuffix "ica"`
`!hassuffix`   |右侧不是左侧的字词后缀     |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右侧是左侧的字词后缀         |是            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右侧不是左侧的字词后缀     |是            |`"North America" !hassuffix_cs "icA"`
`contains`     |右侧作为左侧的子序列出现  |否            |`"FabriKam" contains "BRik"`
`!contains`    |右侧不会在左侧出现           |否            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右侧作为左侧的子序列出现  |是           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |右侧不会在左侧出现           |是           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右侧是左侧的初始子序列|否            |`"Fabrikam" startswith "fab"`
`!startswith`  |右侧不是左侧的初始子序列|否        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右侧是左侧的初始子序列|是            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右侧不是左侧的初始子序列|是        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右侧是左侧的结束子序列|否             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右侧不是左侧的结束子序列|否         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右侧是左侧的结束子序列|是             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右侧不是左侧的结束子序列|是         |`"Fabrikam" !endswith "brik"`
`matches regex`|左侧包含右侧的匹配项        |是           |`"Fabrikam" matches regex "b.*k"`
`in`           |等于某个元素       |是           |`"abc" in ("123", "345", "abc")`
`!in`          |不等于任何元素   |是           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

计算字符串中子字符串的出现次数。 可以匹配纯字符串或使用正则表达式。 纯字符串匹配项可能重叠，而正则表达式匹配项则不会。

### <a name="syntax"></a>语法
```
countof(text, search [, kind])
```

### <a name="arguments"></a>参数：
- `text` - 输入字符串 
- `search` - 用于在文本内部匹配的纯字符串或正则表达式。
- `kind` - _normal_ | _regex_（默认值：normal）。

### <a name="returns"></a>返回

搜索字符串可在容器中匹配的次数。 纯字符串匹配项可能重叠，而正则表达式匹配项则不会。

### <a name="examples"></a>示例

#### <a name="plain-string-matches"></a>纯字符串匹配项

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>正则表达式匹配项

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

从给定的字符串中获取正则表达式的匹配项。 （可选）还可将提取的子字符串转换为指定的类型。

### <a name="syntax"></a>语法

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>参数

- `regex` - 正则表达式。
- `captureGroup` - 指示待提取的捕获组的正整数常量。 0 代表整个匹配项，1 代表正则表达式中第一个“(括号)”匹配的值，2 及以上数字代表后续括号。
- `text` - 要搜索的字符串。
- `typeLiteral` - 可选的类型文本（例如 typeof(long)）。 （如果支持）提取的子字符串将转换成此类型。

### <a name="returns"></a>返回
与指定捕获组 captureGroup 匹配的子字符串可转换为 typeLiteral（可选）。
如果没有匹配项或类型转换失败，则返回 null。

### <a name="examples"></a>示例

以下示例从检测信号记录中提取 *ComputerIP* 的最后一个八位字节：
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

以下示例提取最后一个八位字节，将其强制转换为 *real* 类型（数字），并计算下一个 IP 值
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

以下示例在字符串 *Trace* 中搜索“Duration”的定义。 匹配项强制转换为 *real* 并与时间常量 (1 s) 相乘，该常量将 Duration 强制转换为 timespan 类型。 
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- 如果参数是空字符串或 null，则 *isempty* 返回 true（另请参阅 *isnull*）。
- 如果参数不是空字符串或 null，则 *isnotempty* 返回 true（另请参阅 *isnotnull*）。 别名：*notempty*。

### <a name="syntax"></a>语法

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>示例

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

将 URL 拆分为不同的组成部分（protocol、host、port 等），并返回包含字符串形式的组成部分的字典对象。

### <a name="syntax"></a>语法

```
parseurl(urlstring)
```

### <a name="examples"></a>示例

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

结果将是：
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>replace

将所有正则表达式匹配项替换为另一字符串。 

### <a name="syntax"></a>语法

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>参数

- `regex` - 作为匹配依据的正则表达式。 可在“(括号)”中包含捕获组。
- `rewrite` - 由匹配正则表达式匹配的任何匹配项的替换正则表达式。 使用 \0 引用整个匹配项，使用 \1 引用第一个捕获组，使用 \2 等引用后续捕获组。
- `input_text` - 要在其中搜索的输入字符串。

### <a name="returns"></a>返回值
使用 rewrite 计算结果替换正则表达式的所有匹配项后面的文本。 匹配项不会重叠。

### <a name="examples"></a>示例

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

可能返回以下结果：

activities                                        |替换的内容
------------------------------------------------|----------------------------------------------------------
4663 - 尝试访问某个对象  |活动 ID 4663：尝试访问某个对象。


## <a name="split"></a>split

根据指定的分隔符拆分给定的字符串，并返回生成的子字符串的数组。

### <a name="syntax"></a>语法
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>参数：

- `source` - 要根据指定的分隔符拆分的字符串。
- `delimiter` - 用于拆分源字符串的分隔符。
- `requestedIndex` - 可选的从零开始的索引。 如果已提供，则返回的字符串数组只包含该项（如果存在）。


### <a name="examples"></a>示例

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

连接字符串参数（支持 1-16 个参数）。

### <a name="syntax"></a>语法
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>示例
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

返回字符串的长度。

### <a name="syntax"></a>语法
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>示例
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

从给定的源字符串提取某个子字符串（从指定的索引开始）。 （可选）可以指定请求子字符串的长度。

### <a name="syntax"></a>语法
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>参数：

- `source` - 从中提取子字符串的源字符串。
- `startingIndex` - 请求子字符串的从零开始的起始字符位置。
- `length` - 可选的参数，可用于指定返回的子字符串的请求长度。

### <a name="examples"></a>示例
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower、toupper

将给定的字符串转换为全小写或全大写。

### <a name="syntax"></a>语法
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>示例
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>后续步骤
继续学习高级教程：
* [聚合函数](aggregations.md)
* [高级聚合](advanced-aggregations.md)
* [图表和关系图](charts.md)
* [使用 JSON 和数据结构](json-data-structures.md)
* [高级查询编写](advanced-query-writing.md)
* [联接 - 交叉分析](joins.md)
