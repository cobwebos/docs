---
title: "使用 Azure Machine Learning Workbench 的“按示例拆分列”转换"
description: "“按示例拆分列”转换的参考文档"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 013c99045621e4651a44ab99c9f695fff6004654
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="split-column-by-example-transformation"></a>“按示例拆分列”转换
此转换预先在有意义的边界拆分列内容，无需用户输入。 拆分算法在分析列内容后选择边界。 可以通过以下项定义这些边界
* 固定的分隔符，
* 在特定上下文中出现的多个任意分隔符，或，
* 数据模式或特定实体类型

用户还可以在高级模式下控制拆分行为，其中他们可以指定分隔符或提供所需拆分的示例。

理论上，还可以在 Workbench 中使用一系列*按示例派生列*转换来执行拆分操作。 但是，如果有多列，即使使用示例方法单独派生每个列也会非常耗时。 预先拆分可以轻松实现拆分，用户无需为每列提供示例。 

## <a name="how-to-perform-this-transformation"></a>如何执行此转换

1. 选择想要拆分的列。 
2. 从“转换”菜单中选择“按示例拆分列”。 或者，右键单击所选列的标头，然后从上下文菜单中选择“按示例拆分列”。 转换编辑器随即打开，新列将被添加到所选列旁边。 此时，Workbench 分析输入列，确定拆分边界并合成一个程序，以便拆分在网格中显示的列。 将对列中的所有行执行合成的程序。 最终结果中会排除分隔符（如果有的话）。
3. 可以单击“高级模式”以便更精细地控制拆分转换。 
4. 查看输出并单击“确定”接受转换。 

该转换旨在为所有行生成相同数量的结果列。 如果任何行不能在确定的边界拆分，则默认情况下会为所有列生成 *null*。 可以在“高级模式”下更改此行为。

### <a name="transform-editor-advanced-mode"></a>转换编辑器：高级模式
“高级模式”为拆分列提供了更丰富的体验。 

选择“保留分隔符列”包括最终结果中的分隔符。 默认情况下将会排除分隔符。

指定“分隔符”会替代自动分隔符选择逻辑。 可以指定多个分隔符（每行一个）作为“分隔符”。 所有这些字符都可用作拆分列的分隔符。

有时，在确定的边界拆分值会产生不同于大多数情况的列数。 在这些情况下，“填充方向”用于决定填充列的顺序。

单击“显示建议的示例”可显示用户应为其提供拆分示例的代表行。 用户可以单击建议行右边的“向上”箭头来将该行提升为示例。 

用户可以通过右键单击“示例表”的标头**删除列**或**插入新列**。

为了提供拆分示例，用户可以将值从一个单元格复制并粘贴到另一个单元格。

用户可以通过单击转换编辑器中的链接在“基本模式”和“高级模式”之间切换。

### <a name="editing-an-existing-transformation"></a>编辑现有的转换

用户可以通过选择转换步骤的“编辑”选项编辑现有的“按示例拆分列”转换。 单击“编辑”可在“高级模式”下打开转换编辑器，并显示在创建转换期间提供的所有示例。

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>用固定、单字符分隔符拆分的示例

数据字段通常由单个固定分隔符（例如 CSV 格式的逗号）分隔。 拆分转换尝试自动推断这些分隔符。 例如，在以下方案中，它自动将“.”推断为分隔符。

### <a name="splitting-ip-addresses"></a>拆分 IP 地址

第一列中的值预先拆分为四列。

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>在特定上下文中用多个分隔符拆分的示例

用户的数据可能包括分隔不同字段的许多不同的分隔符。 此外，多次出现的分隔字符串并非每次都是分隔符，仅某些是。 例如在以下情况中，所需的分隔符集是“-”、“,”和“:”以生成所需的输出。 但是，并非每次出现的“:”都是拆分点，因为我们不想拆分时间，而是将其保留在单列中。 拆分转换会根据分隔符出现在输入数据中的上下文推断它们，而不是任何可能出现的分隔符。 转换也会注意常见的数据类型，例如日期和时间。   

### <a name="splitting-store-opening-timings"></a>拆分商店营业计时

以下*计时*列中的值会预先拆分为如下表所示的 9 列。

|计时|
|:-----|
|星期一到星期五：上午 7:00 到下午 6:00，星期六：上午 9:00 到下午 5:00，星期日：休息|
|星期一到星期五：上午 9:00 到下午 6:00，星期六：凌晨 4:00 到下午 4:00，星期日：休息|
|星期一到星期五：上午 8:30 到晚上 7:00，星期六：凌晨 3:00 到下午 2:30，星期日：休息|
|星期一到星期五：上午 8:00 到下午 6:00，星期六：凌晨 2:00 到下午 3:00，星期日：休息|
|星期一到星期五：凌晨 4:00 到晚上 7:00，星期六：上午 9:00 到下午 4:00，星期日：休息|
|星期一到星期五：上午 8:30 到下午 4:30，星期六：上午 9:00 到下午 5:00，星期日：休息|
|星期一到星期五：凌晨 5:30 到下午 6:30，星期六：上午 5:00 到下午 4:00，星期日：休息|
|星期一到星期五：上午 8:30 到晚上 8:30，星期六：凌晨 6:00 到下午 5:00，星期日：休息|
|星期一到星期五：上午 8:00 到晚上 9:00，星期六：上午 9:00 到晚上 8:00，星期日：休息|
|星期一到星期五：上午 10:00 到晚上 9:30，星期六：上午 9:30 到下午 3:00，星期日：休息|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|星期一|星期五|上午 7:00|下午 6:00|星期六|上午 9:00|下午 5:00|星期日|已关闭|
|星期一|星期五|上午 9:00|下午 6:00|星期六|凌晨 4:00|下午 4:00|星期日|已关闭|
|星期一|星期五|上午 8:30|晚上 7:00|星期六|凌晨 3:00|下午 2:30|星期日|已关闭|
|星期一|星期五|上午 8:00|下午 6:00|星期六|凌晨 2:00|下午 3:00|星期日|已关闭|
|星期一|星期五|凌晨 4:00|晚上 7:00|星期六|上午 9:00|下午 4:00|星期日|已关闭|
|星期一|星期五|上午 8:30|下午 4:30|星期六|上午 9:00|下午 5:00|星期日|已关闭|
|星期一|星期五|凌晨 5:30|晚上 6:30|星期六|凌晨 5:00|下午 4:00|星期日|已关闭|
|星期一|星期五|上午 8:30|晚上 8:30|星期六|凌晨 6:00|下午 5:00|星期日|已关闭|
|星期一|星期五|上午 8:00|晚上 9:00|星期六|上午 9:00|晚上 8:00|星期日|已关闭|
|星期一|星期五|上午 10:00|晚上 9:30|星期六|上午 9:30|下午 3:00|星期日|已关闭|

### <a name="splitting-iis-log"></a>拆分 IIS 日志

下面是多个任意分隔符的另一个例子。 此示例还包括上下文分隔符 "/"，不能在 URL 或文件路径内拆分该分隔符。 使用许多“按示例派生列”转换并为每个字段提供示例来执行此拆分是单调乏味的。 使用拆分转换，我们可以执行预先拆分而不提供任何示例。

|日志文本|
|:-----|
|192.128.138.20 - - [16/Oct/2016 16:22:33 -0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (compatible; MSIE 4)" "-"|
|10.128.72.213 - - [17/Oct/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 - - [23/Nov/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 - - [16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 - - [28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)" "-"|
|192.166.64.165 - - [23/Mar/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 - - [16/Apr/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

拆分为：

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16/Oct/2016|16:22:33|-0200|GET|images/picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|compatible; MSIE 4|
|10.128.72.213|17/Oct/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12/Nov/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23/Nov/2016|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|compatible; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23/Mar/2017|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/Apr/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>不用分隔符拆分的示例
在某些情况下，没有实际的分隔符，数据字段可能会彼此相邻。 在这种情况下，拆分转换会自动检测数据中的模式以推断可能的拆分点。 例如，在以下情况下，我们要将金额与货币类型分开，拆分会自动将数字和非数字数据之间的边界推断为拆分点。

### <a name="splitting-amount-with-currency-symbol"></a>将金额和货币符号拆分

|金额|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

在以下示例中，我们要将权重值与度量单位分开。 同样，拆分推断自动检测有意义的边界，并优先于其他可能的分隔符（例如 "." 字符）推断它。 

### <a name="splitting-weights-with-units"></a>使用单位拆分权重

|重量|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5KG|2.5|KG|
|2KG|#N/A|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|#N/A|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>技术说明

拆分转换功能基于**预测程序合成**技术。 在这种技术中，基于输入数据自动学习数据转换程序。 这些程序是用特定领域的语言合成的。 DSL 基于特定正则表达式上下文中出现的分隔符和字段。 有关此技术的更多信息，可以在[最近关于此主题的出版物](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/)中找到。 

