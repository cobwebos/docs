---
title: Azure 数据工厂中映射数据流功能的表达式函数
description: 了解用于映射数据流的表达式函数。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 27d968aa5202fbeb38be9a2416514d2185c1d8b9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436744"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>映射数据流中的数据转换表达式 



## <a name="expression-functions"></a>表达式函数

在数据工厂中，使用映射数据流功能的表达式语言配置数据转换。

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数字的绝对值。
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算余弦反值 * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
添加一对字符串或数字。 将日期添加到日期数。 将持续时间添加到时间戳。 将类似类型的数组追加到另一个数组。 与 + 运算符 * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t @ no__t @ no__t-4 @ no__t @ no__t-8 @ no__t-9<br/><br/>
将日期添加到日期或时间戳。 与日期 @no__t 为 + 运算符相同-0 @ no__t-1 @ no__t-2<br/><br/>
将月份添加到日期或时间戳。 可以选择传递时区 * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
逻辑 AND 运算符。 与 & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
计算反正弦值 * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
计算反切线值 * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
返回平面的正 x 轴与坐标给定的点之间的角度（以弧度表示） * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
获取列的值的平均值 * ``avg(sales)`` @ no__t @ no__t-2<br/><br/>
基于条件获取列的值的平均值 * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
在流中按名称选择列值。 可以将可选流名称作为第二个参数传递。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由一个类型转换函数（TO_DATE，TO_STRING ...）转换的类型。 设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换 * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
根据流中的相对位置（从1开始）选择列值。 如果位置超出界限，则返回 NULL 值。 返回的值必须是由类型转换函数之一转换的类型（TO_DATE，TO_STRING ...）不支持计算输入，但可以使用参数替换 * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
根据备用条件应用一个值或另一个值。 如果输入数为偶数，另一个默认值为 NULL （对于最后一个条件） * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5<br/><br/>
计算数字的 cube root * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
返回不小于 @no__t 数字的最小整数-0 @ no__t-1 @ no__t-2<br/><br/>
返回一组输入中的第一个非 null 值。 所有输入应属于同一类型 * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
比较同一类型的两个值。 如果 value1 < value2，则返回负整数，0 if value1 = = value2，正值 if value1 > value2 * ``(compare(12, 24) < 1) -> true`` @ no__t @ no__t-2 @ no__t<br/><br/>
将字符串的可变数字连接在一起。 与包含字符串的 + 运算符相同 * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
使用分隔符将字符串的可变数字连接在一起。 第一个参数是分隔符 * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
如果所提供的数组中的任何元素在提供的谓词中计算为 true，则返回 true。 Contains 需要引用谓词函数中的一个元素，作为 #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t @ no__t-2 @ no__t<br/><br/>
计算余弦值 * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
计算值的双曲余弦值 * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
获取值的聚合计数。 如果指定了可选列，则它会忽略计数中的 NULL 值 * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5<br/><br/>
获取一组列的非重复值的聚合计数 * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
根据条件获取值的聚合计数。 如果指定了可选列，则将忽略计数中的 NULL 值 * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
获取两个列之间的总体协方差 * ``covariancePopulation(sales, profit)`` @ no__t @ no__t-2<br/><br/>
根据条件，获取两个列的总体协方差 * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
获取两个列的样本协方差 * ``covarianceSample(sales, profit)`` @ no__t @ no__t-2<br/><br/>
基于条件，获取两个列的样本协方差 * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t @ no__t-2<br/><br/>
根据位长度计算不同基元数据类型的列集的 CRC32 哈希，值只能为 0(256)、224、256、384、512。 它可用于计算行 * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2 的指纹<br/><br/>
CumeDist 函数计算某个值相对于分区中所有值的位置。 结果是前面的行数，或者等于当前行在分区中的顺序除以窗口分区中的总行数。 排序中的任何并列值都将计算为相同的位置。
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
当此作业开始运行时获取当前日期。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t @ no__t @-4 @ no__t<br/><br/>
获取在作业开始与本地时区一起运行时的当前时间戳 * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
获取作为 UTC 的当前时间戳。 如果希望在与群集时区不同的时区中解释当前时间，则可以传递格式为 "GMT"、"PST"、"UTC"、"中美洲/开曼" 的可选时区。 它默认为当前时区。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 使用[SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)将 UTC 时间转换为其他时区使用 fromUTC （）。
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期 * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2 的月份日期<br/><br/>
获取给定日期的星期日期。 1-星期日，2-星期一 ...，7-星期六 * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
获取给定日期 @no__t 的一年中的某一天-0 @ no__t-1 @ no__t-2<br/><br/>
天数 @no__t 的持续时间（以毫秒为单位）-0 @ no__t-1 @ no__t-2<br/><br/>
将弧度转换为度 * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
计算某个值在值组中的排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值不会在序列中生成空隙。 即使数据未排序并在值中查找更改 * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2，密集排名仍有效<br/><br/>
将数字对相除。 与/运算符 * ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查字符串是否以提供的字符串结尾 * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
“等于”比较运算符。 等于 = = 运算符 * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
忽略大小写的“等于”比较运算符。 与 < = > 运算符相同 * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
计算数字的阶乘 * ``factorial(5) -> 120`` @ no__t @ no__t-2<br/><br/>
始终返回 false 值。 如果有一个名为 "false" 的列 * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3，则使用函数语法（false （））<br/><br/>
筛选出数组中的元素，这些元素不满足提供的谓词。 Filter 需要引用谓词函数中的一个元素作为 #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
获取列组的第一个值。 如果省略第二个参数 ignoreNulls，则假定为 false * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
返回不大于 @no__t 的最大整数-0 @ no__t-1 @ no__t-2<br/><br/>
转换为 UTC 格式的时间戳。 你可以选择以 "GMT"、"PST"、"UTC"、"中美洲/开曼" 形式传递时区。 它默认为当前 timezoneRefer Java 的 SimpleDateFormat 的可用格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
“大于”比较运算符。 与 > 运算符 * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
“大于等于”比较运算符。 与 > = operator * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
返回值列表中的最大值作为输入跳过 null 值。 如果所有输入均为 null，则返回 null * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5<br/><br/>
按名称检查流中的列值。 可以将可选流名称作为第二个参数传递。  设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换 * ``hasColumn('parent')`` @ no__t-1 @ no__t-2<br/><br/>
获取时间戳的小时值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
小时数的持续时间（以毫秒为单位） * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要引用 expression 函数中的一个元素作为 #item，并将对元素索引的引用作为 #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
根据条件应用一个值或另一个值。 如果未指定其他值，则将其视为 NULL。 这两个值都必须兼容（numeric，string ...） * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
检查该值是否不为 NULL，并返回该值，否则返回该替换值。 它测试所有输入，直到找到第一个非 null 值 * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
检查项是否在数组中 * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
将每个单词的第一个字母转换为大写。 字词标识为由空格分隔 * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
在字符串中查找子字符串的位置（从 1 开始）。 如果找不到 * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4，则返回0<br/><br/>
检查该行是否标记为删除。 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引的默认值为 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查该行是否标记为错误。 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引的默认值为 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查该行是否标记为忽略。 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引的默认值为 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查该行是否标记为插入。 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引的默认值为 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查查找时是否匹配该行。 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引的默认值为 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查值是否为 NULL * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查该行是否标记为更新。 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引的默认值为 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
检查该行是否标记为插入。 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引的默认值为 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
获取列 @no__t 的峰值-0 @ no__t @ no__t-2<br/><br/>
基于条件，获取列 * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 的峰值<br/><br/>
获取当前行之前由第一个参数计算的 n 行的值。 第二个参数是要返回的行数，默认值为1。 如果没有多个行，则返回值为 null 的值，除非指定默认值 * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
获取列组的最后一个值。 如果省略第二个参数 ignoreNulls，则假定为 false * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
获取给定日期 * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2 的月份的最后日期<br/><br/>
获取当前行之后由第一个参数计算的 n 行的值。 第二个参数是要向前看的行数，默认值为1。 如果没有多个行，则返回值为 null 的值，除非指定默认值 * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
“小于等于”比较运算符。 与 < = operator * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
从索引 1 处开始提取包含字符数的子字符串。 与 SUBSTRING （str，1，n） * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
返回字符串的长度 * ``length('dumbo') -> 5`` @ no__t @ no__t-2<br/><br/>
“小于”比较运算符。 与 < 运算符 * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
“小于等于”比较运算符。 与 < = operator * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
获取两个字符串之间的 levenshtein 距离 * ``levenshtein('boys', 'girls') -> 4`` @ no__t @ no__t-2<br/><br/>
模式是按原义匹配的字符串。 以下特殊符号除外： _ 与输入中的任何一个字符匹配（类似于。 posix 正则表达式中的 .）。% 匹配输入中的零个或多个字符（类似于 posix 正则表达式中的 .*）。
转义字符为 ''。 如果转义字符的之前带有特殊符号或其他转义字符，则在字面上匹配后面的字符。 转义其他任何字符的操作无效。
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
从特定的位置开始，在字符串中查找子字符串的位置（从 1 开始）。 如果省略位置，则视为从字符串开头查找。 如果找不到 * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4，则返回0<br/><br/>
计算对数值。 如果使用 @no__t，则可以为其提供一个欧拉数字，如使用-0 @ no__t-1 @ no__t-2<br/><br/>
基于10个基本 @no__t 计算日志值-0 @ no__t-1 @ no__t-2<br/><br/>
Lowercases 字符串 * ``lower('GunChus') -> 'gunchus'`` @ no__t @ no__t-2<br/><br/>
在左侧使用提供的填充内容填充字符串，直到达到特定的长度。 如果字符串等于或大于该长度，则会将其剪裁到长度 * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 "' lpad （" dumbo "、8、" < > "）->" < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
在左侧裁剪掉前导和尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，它将修整第二个参数中指定的任何字符 * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要对 expression 函数中的一个元素引用为 #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
获取列的最大值 * ``max(sales)`` @ no__t @ no__t-2<br/><br/>
根据条件，获取列的最大值 * ``maxIf(region == 'West', sales)`` @ no__t @ no__t-2<br/><br/>
计算不同基元数据类型的列集的 MD5 摘要，并返回 32 字符十六进制字符串。 它可用于计算行 * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2 的指纹<br/><br/>
获取列值的中间值。 与 AVG @no__t 相同-0 @ no__t-1 @ no__t-2<br/><br/>
根据条件获取列值的中间值。 与 avgIf 相同 * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
获取日期的毫秒值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
毫秒数的持续时间（以毫秒为单位） * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
获取列的最小值 * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根据条件，获取列的最小值 * ``minIf(region == 'West', sales)`` @ no__t @ no__t-2<br/><br/>
减去数字。 从日期数中减去日期。 减时间戳的持续时间。 减两个时间戳以毫秒为单位。 与-operator * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
获取时间戳的分钟值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
分钟数的持续时间（以毫秒为单位） * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
将数字对取模。 与% operator @no__t 相同-0 @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
获取日期或时间戳的月份值 * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
获取两个日期之间的月数。 您可以舍入计算。您可以以 "GMT"、"PST"、"UTC"、"中美洲/开曼" 的形式传递可选时区。 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
将数字对相乘。 与 * 运算符 * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
NTile 函数将每个窗口分区的行分割为从 1 到 `n` 的 `n` 桶。 桶值最大相差 1。 如果分区中的行数不能均匀分割成桶数，则余值将逐个分布在每个桶中，从第一个桶开始。 NTile 函数可用于计算 tertiles、四分位数、十分位数和其他常见汇总统计信息。 函数在初始化期间计算两个变量：常规 bucket 的大小将额外添加一行。 这两个变量都以当前分区的大小为基础。 在计算过程中，该函数将跟踪当前行号、当前桶号，以及发生桶更改的行号 (bucketThreshold)。 如果当前行号达到桶的阈值，则桶值将会加 1，阈值将按桶大小增加（如果当前桶已填充，则额外加 1）。
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
对数字求反。 将正数转换为负数，反之亦然 * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
返回下一个唯一序列。 该数字仅在分区内是连续的，并以 partitionId * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2 为前缀<br/><br/>
规范化字符串值以区分重音的 unicode 字符 * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
逻辑求反运算符 * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
“不等于”比较运算符。 与！ = 运算符 * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
返回 NULL 值。 如果存在名为“null”的列，则使用函数 syntax(null())。 使用的任何操作都将导致 NULL * ``isNull('dumbo' + null) -> true`` @ no__t @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
逻辑 OR 运算符。 与 | |* ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
将数字对正数取模。
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
返回输入行在 * ``partitionId()`` @ no__t-1 @ no__t-2 中的当前分区 id<br/><br/>
为另一个 @no__t 的幂引发一个数字-0 @ no__t-1 @ no__t-2<br/><br/>
计算某个值在值组中的排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值将在序列中生成空隙。 即使数据未排序并在值中查找更改 * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
累积数组中的元素。 减少需要引用第一个表达式函数中的累加器和一个元素作为 #acc 和 #item，并且它希望在第二个表达式函数中使用 #result 的结果值 * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
提取给定正则表达式模式的匹配子字符串。 最后一个参数标识匹配组，如果省略，则默认为 1。 使用 "<regex>" （后引号）可匹配不进行转义的字符串 * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t<br/><br/>
检查字符串是否与给定的正则表达式模式相匹配。 使用 "<regex>" （后引号）可匹配不进行转义的字符串 * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t<br/><br/>
将出现的所有正则表达式模式替换为给定字符串中的另一个子字符串使用 "<regex>" （后引号）来匹配一个字符串而不进行转义 * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
基于正则表达式的分隔符拆分字符串，并返回字符串数组 * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t @ no__t-2 @ no__t @ no__t-4 @ no__t-5<br/><br/>
在给定字符串中将出现的所有子字符串替换为另一个子字符串。 如果省略最后一个参数，则默认为空字符串 * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
反转字符串 * ``reverse('gunchus') -> 'suhcnug'`` @ no__t @ no__t-2<br/><br/>
从右侧提取包含字符数的子字符串。 与 SUBSTRING （str，LENGTH （str）-n，n） * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
检查字符串是否与给定的正则表达式模式匹配 * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
在给定可选比例和可选舍入模式的情况下舍入数值。 如果省略小数位数，则默认为0。  如果省略模式，则默认为 ROUND_HALF_UP （5）。 用于舍入的值包括 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t<br/><br/>
为窗口中的行指定顺序行编号，从1开始 * ``rowNumber()`` @ no__t-1 @ no__t-2<br/><br/>
在右侧使用提供的填充内容填充字符串，直到达到特定的长度。 如果字符串等于或大于长度，则将其修整为长度 * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
在右侧裁剪掉前导和尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，它将修整第二个参数中指定的任何字符 * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
获取日期的秒值。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
秒数的持续时间（以毫秒为单位） * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
计算不同基元数据类型的列集的 SHA-1 摘要，并返回 40 字符十六进制字符串。 它可用于计算行 * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2 的指纹<br/><br/>
计算一组具有不同基元数据类型的列的 SHA-1 摘要（给定位长度，只能为值0（256）、224、256、384、512）。 它可用于计算行 * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2 的指纹<br/><br/>
计算正弦值 * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2<br/><br/>
计算双曲正弦值 * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
获取列的偏斜度 * ``skewness(sales)`` @ no__t @ no__t-2<br/><br/>
基于条件，获取列的偏斜度 * ``skewnessIf(region == 'West', sales)`` @ no__t @ no__t-2<br/><br/>
从位置提取数组的子集。 位置从 1 开始。 如果省略长度，则默认为字符串结尾 * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t @ no__t @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
使用提供的谓词函数对数组进行排序。 排序需要引用 expression 函数中的两个连续元素，例如 #item1 和 #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t<br/><br/>
获取字符串的 soundex 代码 * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
基于分隔符拆分字符串并返回字符串数组 * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t @ @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
计算数字的平方根 * ``sqrt(9) -> 3`` @ no__t @ no__t-2<br/><br/>
检查字符串是否以提供的字符串开头 * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
获取列 * ``stdDev(sales)`` @ no__t-1 @ no__t-2 的标准偏差<br/><br/>
基于条件，获取列 * ``stddevIf(region == 'West', sales)`` @ no__t @ no__t-2 列的标准偏差<br/><br/>
获取列的总体标准偏差 * ``stddevPopulation(sales)`` @ no__t @ no__t-2<br/><br/>
基于条件，获取列 * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 列的总体标准偏差<br/><br/>
获取列 @no__t 的样本标准偏差-0 @ no__t @ no__t-2<br/><br/>
基于条件，获取列 * ``stddevSampleIf(region == 'West', sales)`` @ no__t @ no__t-2 列的样本标准偏差<br/><br/>
从日期或时间戳中减去月份。 与日期 * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2 的-运算符相同<br/><br/>
从日期或时间戳减去月份 * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
从某个位置提取特定长度的子字符串。 位置从 1 开始。 如果省略长度，则默认为字符串结尾 * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5<br/><br/>
获取数值列的聚合和 * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
获取数值列的非重复值的聚合和 * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
根据条件获取数字列的聚合总数。 条件可以基于任何列 * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
根据条件获取数字列的聚合总数。 条件可以基于任何列 * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
计算正切值 * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
计算双曲正切值 * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
将任何数字/日期/时间戳/字符串转换为二进制表示形式 * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
将值（"t"、"true"、"y"、"是"、"1"）转换为 true，并将（"f"、"false"、"n"、"no"、"0"）转换为 false，为任何其他值将 NULL 转换为 NULL * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t @ no__t<br/><br/>
使用可选输入日期格式将输入日期字符串转换为日期。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 如果省略输入日期格式，则默认格式为 yyyy-[M] M-[d] d。 接受的格式为： [yyyy，yyyy-[M] M，yyyy-[M] M-[d] d，yyyy-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t @ no__t-2 @ no__t<br/><br/>
将任何数字或字符串转换为小数值。 如果未指定精度和小数位数，则默认为 (10,2)。可以使用可选的 Java 十进制格式进行转换。 采用 BCP47 语言格式的可选区域设置格式，如 en-us、de、zh-chs * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
将任何数字或字符串转换为双精度值。 可以使用可选的 Java 十进制格式进行转换。 采用 BCP47 语言格式的可选区域设置格式，如 en-us、de、zh-chs * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
将任何数字或字符串转换为浮点值。 可以使用可选的 Java 十进制格式进行转换。 截断任何 double * ``toFloat(123.45) -> 123.45f`` @ no__t @ no__t-2 @ no__t @ no__t-4<br/><br/>
将任何数字或字符串转换为整数值。 可以使用可选的 Java 十进制格式进行转换。 截断任何 long、float、double * ``toInteger(123) -> 123`` @ no__t @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
将任何数字或字符串转换为长值。 可以使用可选的 Java 十进制格式进行转换。 截断任何 float，double * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
将任何数字或字符串转换为短值。 可以使用可选的 Java 十进制格式进行转换。 截断任何整数、long、float、double * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
将基元数据类型转换为字符串。 对于数字和日期，可以指定格式。 如果未指定，则选择系统默认值。对数字使用 Java 十进制格式。 有关所有可能的日期格式，请参阅 Java SimpleDateFormat;默认格式为 yyyy-mm-dd * ``toString(10) -> '10'`` @ no__t @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
将字符串转换为给定可选时间戳格式的时间戳。 有关所有可能的格式，请参阅 Java SimpleDateFormat。 如果省略时间戳，则使用默认模式 yyyy-[M] M-[d] d hh： mm： ss [. f ...]。 您可以以 "GMT"、"PST"、"UTC"、"中美洲/开曼" 的形式传递可选时区。Timestamp 支持 999Refer Java 的 SimpleDateFormat 的值，以获得可用格式的最多毫秒准确性。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t @ no__t @ @ no__t @ no__t-6<br/><br/>
将时间戳转换为 UTC。 可以“GMT”、“PST”、“UTC”、“America/Cayman”格式传递可选的时区。 它默认为当前 timezoneRefer Java 的 SimpleDateFormat 的可用格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
将字符串中的一组字符替换为另一组字符。 字符有1到1个替换 * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
裁剪掉前导和尾随字符的字符串。 如果未指定第二个参数，则裁剪掉空格。 否则，它将修整第二个参数中指定的任何字符 * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
始终返回 true 值。 如果有一个名为 "true" 的列 * ``(10 + 20 == 30) -> true`` @ no__t @ no__t-2 @ no__t，则使用函数语法（true （））<br/><br/>
匹配列的类型。 仅可用于模式表达式。数字匹配 short、integer、long、double、float 或 decimal，integer 匹配 short、integer、long、decimal 与 double、float、decimal 和 datetime 匹配 date 或 timestamp type * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__t-sql<br/><br/>
Uppercases 字符串 * ``upper('bojjus') -> 'BOJJUS'`` @ no__t @ no__t-2<br/><br/>
返回生成的 UUID * ``uuid()`` @ no__t-1 @ no__t-2<br/><br/>
获取列 * ``variance(sales)`` @ no__t @ no__t-2 的方差<br/><br/>
基于条件，获取列 * ``varianceIf(region == 'West', sales)`` @ no__t @ no__t-2 列的方差<br/><br/>
获取列 * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2 的总体方差<br/><br/>
基于条件，获取列 * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 列的总体方差<br/><br/>
获取列的无偏差方差 * ``varianceSample(sales)`` @ no__t @ no__t-2<br/><br/>
基于条件，获取列的无偏差方差 * ``varianceSampleIf(region == 'West', sales)`` @ no__t @ no__t-2<br/><br/>
获取给定日期 * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2 的一年中的某一周<br/><br/>
周数的持续时间（以毫秒为单位） * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
逻辑 XOR 运算符。 与 ^ operator @no__t 相同-0 @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
获取日期的年份值 * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>后续步骤

[了解如何使用表达式生成器](concepts-data-flow-expression-builder.md)。
