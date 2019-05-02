---
title: 映射 Data Flow 功能的 Azure 数据工厂中的表达式函数
description: 了解映射数据流中的表达式函数。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 6b4df70114dd481566ae1a666c91c1c9b5bad86f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717013"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>在映射数据流中的数据转换表达式 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>表达式函数

在数据工厂中使用表达式语言映射 Data Flow 功能的配置数据转换。

*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式将标记一对数字正取的模。
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算反余弦值。
* ``acos(1) -> 0.0``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
此表达式将添加一对字符串或数字。 它将日期添加到数天。 它将类似类型的一个数组追加到另一个。 
* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``

**添加**运算符是与相同**+** 运算符。
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
此表达式将天数添加到日期或时间戳。 
* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``

**AddDays**运算符是与相同**+** 日期的运算符。
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
此表达式将几个月添加到日期或时间戳。
* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
这是一个逻辑**和**运算符。 这是与相同**&&** 运算符。
* ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算反正弦值。
* ``asin(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算的反正切值。
* ``atan(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式返回的角度以弧度为单位的一个平面正 x 轴坐标为提供的点之间。
* ``atan2(0, 0) -> 0.0``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式获取列的值的平均值。
* ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
此表达式根据条件，获取列的值的平均值。
* ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>byName</code>
==============================
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
此表达式通过流中的名称选择列的值。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则表达式将返回 NULL 值。 返回的值必须是其中一个类型转换函数 （TO_DATE，TO_STRING...） 的类型转换。在设计时已知的列名称应只需通过其名称进行寻址。 计算的输入不受支持，但可以使用的参数替换。

* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
*********************************
<code>byPosition</code>
==============================
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
此表达式在流中选择某列的值按其相对位置 （从 1 开始）。 如果位于超出界限，则返回 NULL 值。 返回的值必须是其中一个类型转换函数 （TO_DATE、 TO_STRING，等） 的类型转换。 计算的输入不受支持，但可以使用的参数替换。

* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
基于在可选条件上，此表达式所应用或另一个值。 
* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``

如果输入的数字为偶数，另一个值的最后一个条件为 NULL。
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算一个数字的立方根。
* ``cbrt(8) -> 2.0``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式返回不小于数的最小整数。
* ``ceil(-0.1) -> 0``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
此表达式将连接字符串的变量数目。 **Concat**运算符是与相同**+** 运算符与字符串。
* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
此表达式将连接可变数量的字符串和一个分隔符。 第一个参数是分隔符。
* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算余弦值。
* ``cos(10) -> -0.83907152907``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算值的双曲余弦值。
* ``cosh(0) -> 1.0``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
此表达式获取值的聚合计数。 如果可选的列指定，则 NULL 会忽略计数中的值。
* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
此表达式获取的一组列的非重复值的聚合计数。
* ``countDistinct(custId, custName) -> 60``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
根据条件，此表达式获取值的聚合计数。 如果可选的列指定，则 NULL 会忽略在计数中值。
* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取两个列之间的总体协方差。
* ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取两个列的总体协方差。
* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取两个列的样本协方的差。
* ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取两个列的样本协方差。
* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
此表达式将计算的一组不同基元数据类型给出其值可以是唯一 0(256) 的位长度的列的 CRC32 哈希 224、 256、 384、 512。 可以使用**crc32**运算符来计算行的指纹。
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
此函数计算相对于所有值的值的分区中的位置。 结果是前面或等于分区，除以总窗口分区中的行数在排序中的当前行的行数。 在排序中的任何关联值返回相同的位置。
* ``cumeDist() -> 1``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
此表达式获取作业开始运行时的当前日期。 
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``

可以在的窗体中传递可选的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为本地时区。
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
此表达式获取当前时间戳时启动作业以运行与本地时区。
* ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
此表达式获取当前的时间戳为 UTC。 
* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``

可以在的窗体中传递可选的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为本地时区。
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
给定一个日期，此表达式获取每月天数。
* ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
给定一个日期，此表达式获取日期是星期几。 
* ``dayOfWeek(toDate('2018-06-08')) -> 7``

日的值如下所示：
- 1-星期日
- 2-星期一 
- ...
- 7-星期六
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
给定一个日期，此表达式获取一年中的天。
* ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将弧度转换为度。
* ``degrees(3.141592653589793) -> 180``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
此表达式计算一组值中的值的排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值不会生成序列中的空白。 
* ``denseRank(salesQtr, salesAmt) -> 1``

**DenseRank**运算符工作即使数据未排序时。 它会查找更改中的值。
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
此表达式将除以数字的对。 **划分**运算符是与相同**/** 运算符。
* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
此表达式将检查是否与所提供的字符串结尾的字符串。
* ``endsWith('great', 'eat') -> true``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
这是比较等于运算符。 它是与相同**==** 运算符。
* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
**EqualsIgnoreCase**运算符是不区分大小写的比较等于运算符。 它是与相同**<=>** 运算符。
* ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
此表达式将计算一个数字的阶乘。
* ``factorial(5) -> 120``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
此表达式始终返回 false 的值。 
* ``isDiscounted == false()``
* ``isDiscounted() == false``

使用`syntax(false())`函数如果列名*false*。
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
此表达式获取列组的第一个值。 如果省略第二个参数`ignoreNulls`，假定为 false。
* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式返回不大于数的最大整数。
* ``floor(-0.1) -> -1``
*********************************
<code>fromUTC</code>
==============================
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
此表达式将从 UTC 转换为时间戳。 可以选择性地传递的窗体中的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为当前时区。

* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
这是比较**更高版本**运算符。 它是与相同**>** 运算符。
* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
这是一个比较大于或等于运算符。 此运算符等同于**>=** 运算符。
* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
此表达式返回的最大值的值列表作为输入。 如果所有输入都均为 NULL，则返回 NULL。
* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
此表达式获取时间戳的小时值。 
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``

可以在的窗体中传递可选的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为本地时区。
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
基于条件，此表达式所应用或另一个值。 如果未指定其他值，它被视为 NULL。 必须是兼容的这两个值 （数值或字符串，例如）。
* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
此表达式将检查数组中的项。
* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
此表达式将每个单词的第一个字母转换为大写。 由空格分隔标识单词。
* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
此表达式查找字符串中子字符串的位置 （基于 1）。 如果找不到位置，则返回 0。 
* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
此表达式将检查标记为删除的行。 
* ``isDelete() -> true``
* ``isDelete(1) -> false``

如果你转换会执行多个输入的流，则可以传递将索引 （从 1 开始） 的流。 流索引的默认值为 1。
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
此表达式将检查标记为错误的行。
* ``isError() -> true``
* ``isError(1) -> false``

如果你转换会执行多个输入的流，则可以传递将索引 （从 1 开始） 的流。 流索引的默认值为 1。
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
此表达式将检查标记为要忽略的行。
* ``isIgnore() -> true``
* ``isIgnore(1) -> false``

如果你转换会执行多个输入的流，则可以传递将索引 （从 1 开始） 的流。 流索引的默认值为 1。
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
此表达式将检查标记为要插入的行。 
* ``isInsert() -> true``
* ``isInsert(1) -> false``

如果你转换会执行多个输入的流，则可以传递将索引 （从 1 开始） 的流。 流索引的默认值为 1。
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
此表达式将检查在查找匹配的行。 
* ``isMatch() -> true``
* ``isMatch(1) -> false``

如果你转换会执行多个输入的流，则可以传递将索引 （从 1 开始） 的流。 流索引的默认值为 1。
*********************************
<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
此表达式将检查的值为 NULL。
* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
此表达式将检查标记为要更新的行。 
* ``isUpdate() -> true``
* ``isUpdate(1) -> false``

如果你转换会执行多个输入的流，则可以传递将索引 （从 1 开始） 的流。 流索引的默认值为 1。
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的峰值。
* ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的峰值。
* ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
此表达式获取计算的第一个参数的值*n*之前的当前行的行。 第二个参数是要反向查找的行数。 默认值为 1。 如果不存在多个行，除非指定了默认值，则返回的值为 NULL。
* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
此表达式获取列组的最后一个值。 如果省略第二个参数`ignoreNulls`，该表达式返回`false`。
* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
给定一个日期，此表达式获取该月份的最后一个日期。
* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
此表达式获取计算的第一个参数的值*n*之后的当前行的行。 第二个参数是要正向查找的行数。 默认值为 1。 如果不存在多个行，除非指定了默认值，则返回的值为 NULL。
* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
这是一个比较较小或相等运算符。 它是与相同**<=** 运算符。
* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>left</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
此表达式提取位于索引 1 处的子字符串开始，并使用的字符数。 **左**运算符是与相同**的子字符串 (str，1，n)**。
* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
此表达式返回字符串的长度。
* ``length('kiddo') -> 5``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
这是较小的比较的运算符。 它是与相同**<** 运算符。
* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
这是一个比较较小或相等运算符。 它是与相同**<=** 运算符。
* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
此表达式获取两个字符串之间的 Levenshtein 距离。
* ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
在此表达式中，模式是将逐字匹配的字符串。 
* ``like('icecream', 'ice%') -> true``

例外情况包括以下特殊符号：  
* `_` 此匹配输入中的任何一个字符。 它相当于`.`POSIX 正则表达式中。
* `%` 此匹配零个或多个输入中的字符。 此符号是类似于`.*`POSIX 正则表达式中。
* `''` 这是转义字符。 如果转义字符的之前带有特殊符号或其他转义字符，则在字面上匹配后面的字符。 任何其他字符，您不能将其转义。
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
此表达式查找在字符串中，从特定位置开始的子字符串的位置 （基于 1）。 如果省略位置，评估从字符串开头开始。 如果找不到位置，则返回 0。 
* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
此表达式将计算日志值。 你可以提供可选的基或欧拉数，如果使用它。
* ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式使用 10 基来计算日志值。
* ``log10(100) -> 2``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
此表达式设置的字符串以小写形式。
* ``lower('GunChus') -> 'gunchus'``
*********************************
<code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
使用提供的填充，此表达式左侧填充字符串直到字符串达到一定的长度。 如果字符串为等于或大于的长度，则被视为任何操作 （无操作）。
* ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
此表达式左侧修剪前导字符的字符串。 如果未指定第二个参数，该表达式裁剪掉空格。 否则，剪裁的第二个参数指定的字符。
* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
此表达式获取列的最大值。
* ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
此表达式根据条件，获取列的最大值。
* ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
此表达式将计算的一组不同的基元数据类型的列的 MD5 摘要。 它返回 32 个字符的十六进制字符串。 
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``

可以使用**md5**运算符来计算行的指纹。
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式获取列的值的平均值。 **意味着**运算符等同于 avg.
* ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
此表达式根据条件，获取列的值的平均值。 **MeanIf**运算符是与相同**avgIf**。
* ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
此表达式获取列的最小值。
* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
此表达式根据条件，获取列的最小值。
* ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
此表达式数字相减。 例如，它可减去一个日期的天数。 **减**运算符是与相同**-** 运算符。
* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
此表达式获取时间戳的分钟值。 
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``

可以在的窗体中传递可选的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为本地时区。
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
**Mod**运算符将标记一取模对数字。 这是与相同**%** 运算符。
* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
此表达式获取日期或时间戳的月份值。
* ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
此表达式获取两个日期之间的月的数。 
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

可以在的窗体中传递可选的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为本地时区。
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
此表达式将相乘数字的对。 **乘**运算符等同于 * 运算符。
* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
**NTile**函数将为每个窗口分区到行*n*范围从 1 到最多的存储桶*n*。 存储桶值最多 1 而有所不同。 如果分区中的行数不均匀划分的存储桶数，每个剩余的值将分布到存储桶，第一个存储桶的开始。 

* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``

**NTile**时您需要计算 tertiles、 四分位数、 十分位数和其他常见的摘要统计信息函数很有用。 在初始化期间，该函数计算两个变量。 额外添加一列添加到常规的存储桶中。 这两个变量基于当前分区的大小。 

计算过程中，该函数将跟踪的当前行号、 当前的存储桶数，并在其中存储桶会更改 (bucketThreshold) 的行号。 当行数达到桶阈值时，则存储桶值会增加 1。 阈值如果将增加存储桶大小，加上一个额外填充当前存储桶。
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式对数字求反。 将正数负数，反之亦然。
* ``negate(13) -> -13``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
此表达式将返回下一个唯一的序列。 仅在一个分区内连续数。 其前缀为`partitionId`。
* ``nextSequence() -> 12313112``
*********************************
<code>normalize</code>
==============================
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
规范化为单独的带重音符的 unicode 字符的字符串值 * ``normalize('boys') -> 'boys'``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
**不**运算符是一个逻辑求反运算符。
* ``not(true) -> false``
* ``not(premium)``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
**NotEquals**运算符是比较不等于运算符。 它是与相同 **！ =** 运算符。
* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
此表达式返回一个 NULL 值。 
* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``

使用函数**syntax(null())** 如果列名*null*。 使用 NULL 运算符的任何操作会导致 NULL。
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
**或**运算符是一个逻辑**OR**运算符。 它是与相同**||** 运算符。
* ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
**PMod**运算符将标记的一对数字正取模。
* ``pmod(-20, 8) -> 4``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式求幂的另一个的一个数字。
* ``power(10, 2) -> 100``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
此表达式计算一组值中的值的排名。 结果是 1 加上前面的行数，或者等于当前行在分区中的顺序。 值生成序列中的空白。 
* ``rank(salesQtr, salesAmt) -> 1``

**排名**运算符工作即使数据未排序时。 它会查找值中的更改。
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
此表达式中提取给定正则表达式模式匹配的子字符串。 最后一个参数标识的匹配项组。 如果省略最后一个参数，默认值为 1。 
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

使用`<regex>`（反引号） 来匹配而不转义的字符串。
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
此表达式将检查字符串是否与给定正则表达式模式匹配。 
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

使用`<regex>`（反引号） 来匹配而不转义的字符串。
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
此表达式用给定字符串中的另一个子字符串替换所有匹配的正则表达式模式。
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

使用`<regex>`（反引号） 来匹配而不转义的字符串。
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
此表达式将拆分基于根据正则表达式分隔符的字符串。 它返回一个字符串数组。
* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
此表达式用给定字符串中的另一个子字符串替换所有出现的子字符串。
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
此表达式可反转一个字符串。
* ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
此表达式从右侧中提取子字符串的字符数。 
* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``

**右**函数等同于 (str，LENGTH(str)-n，n) 的子字符串。
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
此表达式将检查字符串是否与给定正则表达式模式匹配。
* ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
此表达式提供可选的小数位数和可选的舍入模式，舍入数值。 如果省略规模，默认值为 0。 如果省略模式下，默认值为 ROUND_HALF_UP(5)。 

* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``

以下是用于舍入值：
* 1-ROUND_UP
* 2-ROUND_DOWN
* 3-ROUND_CEILING
* 4-ROUND_FLOOR
* 5-ROUND_HALF_UP
* 6 - ROUND_HALF_DOWN
* 7 - ROUND_HALF_EVEN
* 8-ROUND_UNNECESSARY

*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
此表达式将分配顺序行的行在窗口中，从 1 开始编号。
* ``rowNumber() -> 1``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
此表达式右侧填充的字符串之前提供的空白字符串达到一定的长度。 如果字符串为等于或大于的长度，则被视为任何操作 （无操作）。
* ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
<code>rtrim</code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
此表达式右侧修剪前导字符的字符串。 如果未指定第二个参数，该表达式裁剪掉空格。 否则，剪裁的第二个参数指定的任何字符。
* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
此表达式获取一个日期的第二个值。 
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``

可以在的窗体中传递可选的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为本地时区。
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
此表达式将计算的一组不同的基元数据类型的列的 sha-1 摘要。 它返回 40 个字符的十六进制字符串。 
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``

可以使用`sha1`来计算行的指纹。
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
此表达式将计算的一组不同基元数据类型给出其值可以是仅 0(256)，位长度的列的 sha-2 摘要 224、 256、 384、 512。 
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``

可以使用`sha2`来计算行的指纹。
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算正弦值。
* ``sin(2) -> 0.90929742682``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算双曲正弦值。
* ``sinh(0) -> 0.0``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的不对称度。
* ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的不对称度。
* ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
此表达式从一个位置中提取数组的子集。 位置基于 1。 如果省略长度，则默认值为字符串的末尾。
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
此表达式获取的字符串的 soundex 代码。
* ``soundex('genius') -> 'G520'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
此表达式将拆分基于分隔符的字符串。 它返回一个字符串数组。
* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式计算一个数字的平方根。
* ``sqrt(9) -> 3``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
此表达式将检查字符串是否以所提供的字符串开头。
* ``startsWith('great', 'gr') -> true``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的标准偏差。
* ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的标准偏差。
* ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的总体标准偏差。
* ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的总体标准偏差。
* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的样本标准偏差。
* ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的样本标准偏差。
* ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
此表达式中减去从日期的月数。 
* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
**SubDays**运算符是与相同**-** 日期的运算符。
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
此表达式中减去从日期或时间戳的几个月。
* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
此表达式从一个位置中提取的特定长度的子字符串。 位置基于 1。 如果省略长度，则默认值为字符串的末尾。
* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式获取数值列的聚合总数。
* ``sum(col) -> value``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
此表达式获取数值列的非重复值的聚合总数。
* ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
此表达式根据条件，获取数值列的聚合总数。 条件可以基于任何列。
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
此表达式根据条件，获取数值列的聚合总数。 条件可以基于任何列。
* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算正切值。
* ``tan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式将计算双曲正切值。
* ``tanh(0) -> 0.0``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
此表达式将转换的值为`('t', 'true', 'y', 'yes', '1')`为 true。 它将为`('f', 'false', 'n', 'no', '0')`为 false。 对于任何其他值，则返回 NULL。
* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
给定的可选日期格式，此表达式将字符串转换为日期。 所有可能的日期格式，请参阅 Java SimpleDateFormat。 
* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``

如果省略日期格式，则接受以下项的组合: [yyyy，yyyy-[M] M，yyyy [d]-[M] M-d，yyyy-[M] M-[d] d，yyyy-[M] M-[d] d，yyyy-[M] M-[d] dT *]。
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
此表达式将任何数值或字符串转换为十进制值。 
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

如果未指定精度和小数位数，默认值为 (10,2)。 用于转换，可以使用可选的 Java 十进制格式。

*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
此表达式将任何数值或字符串转换为双精度值。 用于转换，可以使用可选的 Java 十进制格式。
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
此表达式将任何数值或字符串转换为浮点值。 用于转换，可以使用可选的 Java 十进制格式。 
* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``

**ToFloat**函数将截断任何双。
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
此表达式将任何数值或字符串转换为一个整数值。 用于转换，可以使用可选的 Java 十进制格式。 
* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``

**ToInteger**函数将截断任何 long、 float、 或双引号。
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
此表达式将任何数值或字符串转换为长整型值。 用于转换，可以使用可选的 Java 十进制格式。 
* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``

**ToLong**函数将截断任何 float 或 double。
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
此表达式将任何数值或字符串转换为较短的值。 用于转换，可以使用可选的 Java 十进制格式。 
* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``

**ToShort**函数将截断任何整数、 long、 float、 或双引号。
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
此表达式将基元数据类型转换为字符串。 
* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``

可以指定数字和日期的格式。 如果未指定一种格式，则使用系统默认值。 默认格式是`yyyy-MM-dd`。 号遵循 Java 十进制格式。 对于所有可能的日期格式，请参阅 Java SimpleDateFormat。 
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
给定的可选时间戳格式，此表达式将字符串转换为日期。 
* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``

所有可能的格式，请参阅 Java SimpleDateFormat。 如果忽略时间戳的默认模式用于`yyyy-[M]M-[d]d hh:mm:ss[.f...]`使用。
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
此表达式将转换为 UTC 时间戳。 
* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``

可以在的窗体中传递可选的时区`'GMT'`， `'PST'`， `'UTC'`， `'America/Cayman'`。 默认值为当前所在的时区。
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
此表达式替换另一组字符在字符串中的一组字符。 字符具有一对一的替换。
* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
此表达式裁剪掉前导和尾随字符的字符串。 
* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``

如果未指定第二个参数，该函数裁剪掉空格。 否则，剪裁的第二个参数指定的任何字符。

*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
此表达式始终返回`true`值。  
* ``isDiscounted == true()``
* ``isDiscounted() == true``

如果列名为 *，则返回 true*，使用函数**syntax(true())**。
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
此表达式匹配的列的类型。 
* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``

仅在模式表达式中使用此函数： 数与 short、 integer、 long、 双精度、 float 或 （十进制） 整数匹配简单地说，整数、 双精度的长而小数部分匹配项、 float、 decimal、 和 datetime 的匹配项的日期或时间戳类型。
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
此表达式设置的字符串以大写形式。
* ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的方差。
* ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的方差。
* ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的总体方差。
* ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的总体方差。
* ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
此表达式获取列的无偏差方差。
* ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
此表达式根据条件，获取列的无偏差方差。
* ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
给定一个日期，此表达式获取一年中的周。
* ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
此表达式使用的逻辑**xor**运算符。 此运算符等同于**^** 运算符。
* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
给定一个日期，此表达式获取的年份值。
* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>后续步骤

[了解如何使用表达式生成器](concepts-data-flow-expression-builder.md)。
