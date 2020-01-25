---
title: 映射数据流中的表达式函数
description: 了解用于映射数据流的表达式函数。
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2019
ms.openlocfilehash: 68771ee3d2ae2d43245bd217bedcf59b987786f1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716736"
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
计算 * ``acos(1) -> 0.0``
___
### <code>add</code>的余弦反值 
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
문자열 또는 숫자의 쌍을 추가합니다. 날짜에 일 수를 추가합니다. 将持续时间添加到时间戳。 유사한 형식의 한 배열을 다른 배열에 추가합니다. 与 + 运算符 * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
날짜 또는 타임스탬프에 일을 추가합니다. 与日期 * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>的 + 运算符相同 
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
将月份添加到日期或时间戳。 您可以选择传递时区 * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리적 AND 연산자입니다. 与 & & * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``asin(0) -> 0.0``
___
### <code>atan</code>的反正弦值 
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``atan(0) -> 0.0``
___
### <code>atan2</code>的反正切值 
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
返回平面的正 x 轴和坐标给定的点之间的角度（以弧度表示） * ``atan2(0, 0) -> 0.0``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
在流中按名称选择列值。 可以将可选流名称作为第二个参数传递。 如果有多个匹配项，则返回第一个匹配项。 如果没有匹配项，则返回 NULL 值。 返回的值必须是由某种类型转换函数（TO_DATE、TO_STRING ...）转换的类型。 设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以使用参数替换 * ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))``
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
根据流中的相对位置（从1开始）选择列值。 如果位置超出界限，则返回 NULL 值。 返回的值必须是由类型转换函数之一转换的类型（TO_DATE，TO_STRING ...）不支持计算输入，但可以 
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))``
* ``toString(byPosition(1234))``
___
### <code>case</code>使用参数替换 * ``toString(byPosition(1))``
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
교대로 나오는 조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 如果输入数为偶数，另一个默认值为 NULL （对于最后一个条件 * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算数字 * ``cbrt(8) -> 2.0``
___
### <code>ceil</code>的立方根 
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
返回不小于 * ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>数量的最小整数 
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
返回一组输入中的第一个非 null 值。 所有输入都应具有相同的类型 * ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
比较同一类型的两个值。 如果 value1 < value2，则返回负整数; 如果 value1 = = value2，则为 0; 如果 value1 > value2 * ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>，则返回正值 
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
가변 개수의 문자열을 함께 연결합니다. 문자열이 있는 + 연산자와 같습니다. * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
가변 개수의 문자열을 구분 기호와 함께 연결합니다. 第一个参数是 
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>contains</code>* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``分隔符 
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
如果所提供的数组中的任何元素在提供的谓词中计算为 true，则返回 true。 Contains 需要引用谓词函数中的一个元素，作为 #item * ``contains([1, 2, 3, 4], #item == 3) -> true``
* ``contains([1, 2, 3, 4], #item > 5) -> false``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``cos(10) -> -0.8390715290764524``
___
### <code>cosh</code>的余弦值 
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``cosh(0) -> 1.0``
___
### <code>crc32</code>的值的双曲余弦值 
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
0(256), 224, 256, 384, 512 값의 지정된 비트 길이를 갖는 다양한 기본 데이터 형식의 열 세트에서 CRC32 해시를 계산합니다. 它可用于计算行 * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>currentDate</code>的指纹 
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
이 작업 실행이 시작되는 현재 날짜를 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
获取在本地时区 * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>运行时的当前时间戳 
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
获取作为 UTC 的当前时间戳。 如果希望在与群集时区不同的时区中解释当前时间，则可以传递格式为 "GMT"、"PST"、"UTC"、"中美洲/开曼" 的可选时区。 它默认为当前时区。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)를 참조하세요. 若要将 UTC 时间转换为其他时区，请使用 fromUTC （） * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期 * ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>的月份日期 
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
지정된 날짜의 요일을 가져옵니다. 1-星期日、2-星期一 ...、7-星期六 * ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期 * ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>的一年中的某一天 
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>degrees</code>* ``days(2) -> 172800000L``天数的持续时间（以毫秒为单位） 
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
将弧度转换为度 * ``degrees(3.141592653589793) -> 180``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍을 나눕니다. 与/operator * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>相同 
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
检查字符串是否以提供的字符串结尾 * ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 같음 연산자. 与 = = 运算符 * ``equals(12, 24) -> false``
* ``12 == 24 -> false``
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
* ``isNull(null == null) -> true``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
비교는 대/소문자를 무시하는 연산자와 같습니다. 与 < = > operator * ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
计算数字 * ``factorial(5) -> 120``
___
### <code>false</code>的阶乘 
<code><b>false() => boolean</b></code><br/><br/>
항상 false 값을 반환합니다. 如果有一个名为 "false" 的列 * ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>，请使用函数语法（false （）） 
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
筛选出数组中的元素，这些元素不满足提供的谓词。 Filter 需要引用谓词函数中的一个元素作为 #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
返回 * ``floor(-0.1) -> -1``
___
### <code>fromBase64</code>不大于的最大整数 
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
在 base64 * ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``
___
### <code>fromUTC</code>中编码给定的字符串 
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
转换为 UTC 格式的时间戳。 你可以选择以 "GMT"、"PST"、"UTC"、"中美洲/开曼" 形式传递时区。 它默认为当前 timezoneRefer Java 的 SimpleDateFormat 的可用格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 큼 연산자. 与 > 运算符 * ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 크거나 같음 연산자. 与 > = 运算符 * ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``的 
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
返回值列表中的最大值作为输入跳过 null 值。 如果所有输入均为 null * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>，则返回 null 
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
按名称检查流中的列值。 可以将可选流名称作为第二个参数传递。  设计时已知的列名应该只按名称进行寻址。 不支持计算输入，但可以 * ``hasColumn('parent')``
___
### <code>hour</code>使用参数替换 
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
타임스탬프의 시간 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
小时数的持续时间（以毫秒为单位） * ``hours(2) -> 7200000L``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 如果未指定其他值，则将其视为 NULL。 这两个值都必须兼容（数值、字符串 ...） * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
检查该值是否不为 NULL，并返回该值，否则返回该替换值。 它测试所有输入，直到找到第一个非 null 值 * ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
检查项是否存在于数组中 * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
모든 단어의 첫 글자를 대문자로 변환합니다. 单词被标识为由空白 * ``initCap('cool iceCREAM') -> 'Cool Icecream'``
___
### <code>instr</code>分隔 
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 如果 
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>中找不到 * ``instr('dumbo', 'mbo') -> 3``，则返回 0 
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삭제용으로 표시되어 있는지 확인합니다. 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引应为1或2，默认值为 1 * ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 오류로 표시되는지 여부를 확인합니다. 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引应为1或2，默认值为 1 * ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 무시되도록 표시되는지 여부를 확인합니다. 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引应为1或2，默认值为 1 * ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引应为1或2，默认值为 1 * ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
조회 시 행이 일치하는지 여부를 확인합니다. 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引应为1或2，默认值为 1 * ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
检查值是否为 NULL * ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 업데이트용으로 표시되어 있는지 확인합니다. 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引应为1或2，默认值为 1 * ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 对于采用多个输入流的转换，可以传递流的（从1开始）索引。 流索引应为1或2，默认值为 1 * ``isUpsert()``
* ``isUpsert(1)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
获取给定日期 * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>least</code>的月份的最后日期 
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
비교 보다 작거나 같음 연산자. 与 < = 运算符 * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``的 
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
인덱스 1에서 시작하여 지정된 문자 수를 갖는 부분 문자열을 추출합니다. 与 SUBSTRING （str，1，n） * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>相同 
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
返回字符串 * ``length('dumbo') -> 5``
___
### <code>lesser</code>的长度 
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 작음 연산자. 与 < 运算符 * ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 작거나 같음 연산자. 与 < = 运算符 * ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``的 
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
获取 * ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>两个字符串之间的 levenshtein 距离 
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
模式是按原义匹配的字符串。 以下特殊符号除外： _ 与输入中的任何一个字符匹配（类似于。 .과 유사). %는 입력의 0개 이상의 문자와 일치합니다(posix 정규식의 .*와 유사).
이스케이프 문자는 ''입니다. 특수 기호 또는 다른 이스케이프 문자 앞에 이스케이프 문자가 오면 다음 문자는 글자 그대로 일치됩니다. 다른 모든 문자를 이스케이프하는 것은 유효하지 않습니다.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
특정 위치에서 시작하는 문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 이 위치를 생략하면 문자열의 시작 부분에서 찾습니다. 如果 
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>中找不到 * ``locate('mbo', 'dumbo') -> 3``，则返回 0 
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
로그 값을 계산합니다. 如果使用 * ``log(100, 10) -> 2``
___
### <code>log10</code>，则可以提供可选的基，如 
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
基于10个基本 * ``log10(100) -> 2``
___
### <code>lower</code>计算日志值 
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lowercases 
___
### <code>lpad</code>字符串 * ``lower('GunChus') -> 'gunchus'``
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 왼쪽 여백을 채웁니다. 如果字符串等于或大于长度，则会将其修整为长度 * ``lpad('dumbo', 10, '-') -> '-----dumbo'``
* ``lpad('dumbo', 4, '-') -> 'dumb'``
* "" lpad （"dumbo"、"8"、"< >"）-> "< ><dumbo'``
___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
왼쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 否则，它会将第二个参数中指定的任何字符 * ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>map</code>中 
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要引用 expression 函数中的一个元素作为 #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
使用提供的表达式将数组的每个元素映射到新元素。 Map 需要引用 expression 函数中的一个元素作为 #item，并对元素索引的引用作为 #index * ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
다양한 기본 데이터 형식의 열 세트에서 MD5 다이제스트를 계산하고 32자의 16진수 문자열을 반환합니다. 它可用于计算行 * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>millisecond</code>的指纹 
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
获取日期的毫秒值。 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
以毫秒为单位的持续时间（以毫秒为单位） * ``milliseconds(2) -> 2L``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자를 뺍니다. 날짜에서 일 수를 뺍니다. 减时间戳的持续时间。 减两个时间戳以毫秒为单位。 与-operator * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
타임스탬프의 분 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
* ``minutes(2) -> 120000L``
___
### <code>mod</code>的分钟数（以毫秒为单位） 
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍의 모듈러스입니다. 与% operator * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>相同 
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取 * ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>的日期或时间戳的月份值 
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
获取两个日期之间的月数。 您可以舍入计算。您可以以 "GMT"、"PST"、"UTC"、"中美洲/开曼" 的形式传递可选时区。 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍을 곱합니다. 与 * 运算符 * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>negate</code>相同 
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자를 부정합니다. 将正数转换为负数，反之亦然 * ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
고유한 다음 시퀀스를 반환합니다. 该数字仅在分区内是连续的，并以 partitionId * ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>为前缀 
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
规范化字符串值以区分重音的 unicode 字符 * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
逻辑求反运算符 * ``not(true) -> false``
* ``not(10 == 20) -> true``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 같지 않음 연산자. 与！ = 运算符 * ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>notNull</code>相同 
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
检查值是否不为 NULL * ``notNull(NULL()) -> false``
* ``notNull('') -> true``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL 값을 반환합니다. 'Null'라는 열이 있는 경우 함수 syntax(null())를 사용합니다. 使用的任何操作都将导致 NULL * ``isNull('dumbo' + null) -> true``
* ``isNull(10 * null) -> true``
* ``isNull('') -> false``
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리적 OR 연산자입니다. 与 | |* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍의 양의 모듈러스입니다.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
返回输入行 * ``partitionId()``
___
### <code>power</code>的当前分区 id 
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
为另一个 * ``power(10, 2) -> 100``
___
### <code>reduce</code>的幂引发一个数字 
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
累积数组中的元素。 减少需要引用第一个表达式函数中的累加器和一个元素作为 #acc 和 #item，并且它希望在第二个表达式函数中使用 #result * ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
지정된 정규식 패턴에 대해 일치하는 부분 문자열을 추출합니다. 마지막 매개 변수는 일치 그룹을 식별하고 생략하면 기본적으로 1이 지정됩니다. 使用 "<regex>" （后引号）可匹配不 
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>转义的字符串 * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 지정된 정규식 패턴과 일치하는지 확인합니다. 使用 "<regex>" （后引号）可匹配不 
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>转义的字符串 * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
将出现的所有正则表达式模式替换为给定字符串中的另一个子字符串使用 "<regex>" （后引号）来匹配一个字符串，而不 
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>进行转义 * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
基于正则表达式的分隔符拆分字符串，并返回字符串数组 * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
在给定字符串中将出现的所有子字符串替换为另一个子字符串。 如果省略最后一个参数，则默认为空字符串 * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
反转字符串 * ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
오른쪽부터 지정된 문자 수를 갖는 부분 문자열을 추출합니다. 与 SUBSTRING （str，LENGTH （str）-n，n）相同 * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
检查字符串是否与给定的正则表达式模式匹配 * ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
在给定可选比例和可选舍入模式的情况下舍入数值。 如果省略小数位数，则默认为0。  如果省略模式，则默认为 ROUND_HALF_UP （5）。 舍入的值包括 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR ROUND_HALF_UP ROUND_HALF_DOWN ROUND_HALF_EVEN ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 오른쪽 여백을 채웁니다. 如果字符串等于或大于该长度，则将其剪裁到 * ``rpad('dumbo', 10, '-') -> 'dumbo-----'``
* ``rpad('dumbo', 4, '-') -> 'dumb'``
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``
___
### <code>rtrim</code>rtrim</code>的长度 
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
오른쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 否则，它会将第二个参数中指定的任何字符 * ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>中 
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
날짜의 초 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 本地时区用作默认时区。有关可用格式，请参阅 Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
* ``seconds(2) -> 2000L``
___
### <code>sha1</code>的秒数的持续时间（以毫秒为单位） 
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
다양한 기본 데이터 형식의 열 세트에서 SHA-1 다이제스트를 계산하고 40자의 16진수 문자열을 반환합니다. 它可用于计算行 * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>的指纹 
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
计算一组具有不同基元数据类型的列的 SHA-1 摘要（给定位长度，只能为值0（256）、224、256、384、512）。 它可用于计算行 * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>的指纹 
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``sin(2) -> 0.9092974268256817``
___
### <code>sinh</code>的正弦值 
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``sinh(0) -> 0.0``
___
### <code>slice</code>的双曲正弦值 
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
특정 위치에서 배열의 하위 세트를 추출합니다. 위치는 1부터 시작합니다. 如果省略长度，则默认为字符串末尾的 * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
使用提供的谓词函数对数组进行排序。 排序需要引用 expression 函数中的两个连续元素，#item1 和 #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
获取字符串的 soundex 代码 * ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
基于分隔符拆分字符串并返回字符串数组，* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``sqrt(9) -> 3``
___
### <code>startsWith</code>数值的平方根 
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
检查字符串是否以提供的字符串 
___
### <code>subDays</code>开头 * ``startsWith('dumbo', 'du') -> true``
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
从日期或时间戳中减去月份。 与 * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>日期的-operator 相同 
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
从日期或时间戳减去月份 * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
특정 위치에서 특정 길이의 부분 문자열을 추출합니다. 위치는 1부터 시작합니다. 如果省略长度，则默认为字符串末尾的 * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``tan(0) -> 0.0``
___
### <code>tanh</code>的正切值 
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
计算 * ``tanh(0) -> 0.0``
___
### <code>toBase64</code>的双曲正切值 
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
在 base64 * ``toBase64('bojjus') -> 'Ym9qanVz'``
___
### <code>toBinary</code>中编码给定的字符串 
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
将任何数字/日期/时间戳/字符串转换为二进制表示形式 * ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
将值（"t"、"true"、"y"、"yes"、"1"）转换为 true，并将（"f"、"false"、"n"、"no"、"0"）转换为 false，为任何其他值 * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
使用可选输入日期格式将输入日期字符串转换为日期。 有关可用格式，请参阅 Java 的 SimpleDateFormat。 如果省略输入日期格式，则默认格式为 yyyy-[M] M-[d] d。 接受的格式为： [yyyy，yyyy-[M] M，yyyy-[M] M-[d] d，yyyy-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
숫자 또는 문자열을 10진수 값으로 변환합니다. 자릿수와 소수 자릿수를 지정하지 않으면 기본값인 (10,2)가 사용됩니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 采用 BCP47 语言形式的可选区域设置格式，如 en-us、de、zh-chs * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
숫자 또는 문자열을 배정도 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 采用 BCP47 语言形式的可选区域设置格式，如 en-us、de、zh-chs * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
숫자 또는 문자열을 부동 소수점 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 截断任何 double * ``toFloat(123.45) -> 123.45f``
* ``toFloat('123.45') -> 123.45f``
* ``toFloat('$123.45', '$###.00') -> 123.45f``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
将任何数字或字符串转换为整数值。 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 截断任意 long、float、double * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
숫자 또는 문자열을 긴 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 截断任意 float、double * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
숫자 또는 문자열을 짧은 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 截断所有整数、long、float、double * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
기본 데이터 형식을 문자열로 변환합니다. 숫자 및 날짜의 경우 형식을 지정할 수 있습니다. 지정하지 않으면 시스템 기본값은 picked.Java입니다. 숫자에는 10진수 형식이 사용됩니다. 有关所有可能的日期格式，请参阅 Java SimpleDateFormat;默认格式为 yyyy-mm-dd * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
将字符串转换为给定可选时间戳格式的时间戳。 有关所有可能的格式，请参阅 Java SimpleDateFormat。 如果省略时间戳，则使用默认模式 yyyy-[M] M-[d] d hh： mm： ss [. f ...]。 您可以以 "GMT"、"PST"、"UTC"、"中美洲/开曼" 的形式传递可选时区。对于可用格式，时间戳最多支持毫秒准确性，值为 999Refer Java 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
타임스탬프를 UTC로 변환합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 它默认为当前 timezoneRefer Java 的 SimpleDateFormat 的可用格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
하나의 문자 세트를 문자열의 다른 문자 세트로 바꿉니다. 字符的替换 * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``为1到 1 
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
선행 및 후행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 否则，它会将第二个参数中指定的任何字符 * ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>中 
<code><b>true() => boolean</b></code><br/><br/>
항상 true 값을 반환합니다. 如果有一个名为 "true" 的列 * ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>，则使用函数语法（true （）） 
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
열의 형식과 일치합니다. 仅可用于模式表达式。数字匹配 short、integer、long、double、float 或 decimal，整数与 short、integer、long、decimal 匹配 double、float、decimal 和 datetime 匹配 date 或 timestamp type * ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Uppercases 
___
### <code>uuid</code>字符串 * ``upper('bojjus') -> 'BOJJUS'``
<code><b>uuid() => string</b></code><br/><br/>
返回生成的 UUID * ``uuid()``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取给定日期 * ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>的一年中的哪一周 
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
* ``weeks(2) -> 1209600000L``
___
### <code>xor</code>的周数的持续时间（以毫秒为单位） 
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리 XOR 연산자입니다. 与 ^ operator * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
获取 * ``year(toDate('2012-8-8')) -> 2012``
## 聚合函数的日期的年份值。以下函数仅可用于聚合、透视、逆透视和窗口转换 ___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
获取列 * ``avg(sales)``
___
### <code>avgIf</code>的平均值 
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
基于条件获取列 * ``avgIf(region == 'West', sales)``
___
### <code>count</code>的平均值 
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
값의 집계 개수를 가져옵니다. 如果指定了可选列，则将忽略计数 * ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>中的 NULL 值 
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
获取 * ``countDistinct(custId, custName)``
___
### <code>countIf</code>的一组列的非重复值的聚合计数 
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
조건에 따라 값의 집계 개수를 가져옵니다. 如果指定了可选列，则将忽略计数 * ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>中的 NULL 值 
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
获取 * ``covariancePopulation(sales, profit)``
___
### <code>covariancePopulationIf</code>的两个列之间的总体协方差 
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取 * ``covariancePopulationIf(region == 'West', sales)``
___
### <code>covarianceSample</code>的两个列的总体协方差 
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
获取 * ``covarianceSample(sales, profit)``
___
### <code>covarianceSampleIf</code>两个列的样本协方差 
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取 * ``covarianceSampleIf(region == 'West', sales, profit)``
___
### <code>first</code>两个列的样本协方差 
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
열 그룹의 첫 번째 값을 가져옵니다. 如果省略第二个参数 ignoreNulls，将假定 
* ``first(sales, false)``* ``first(sales)``为 false 
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``kurtosis(sales)``
___
### <code>kurtosisIf</code>的峰值 
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``kurtosisIf(region == 'West', sales)``
___
### <code>last</code>的峰值 
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
열 그룹의 마지막 값을 가져옵니다. 如果省略第二个参数 ignoreNulls，将假定 
* ``last(sales, false)``* ``last(sales)``为 false 
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
获取 * ``max(sales)``
___
### <code>maxIf</code>的列的最大值 
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
基于条件，获取 * ``maxIf(region == 'West', sales)``
___
### <code>mean</code>的列的最大值 
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
열 값의 평균값을 가져옵니다. 与 AVG * ``mean(sales)``
___
### <code>meanIf</code>相同 
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
조건에 따라 열 값의 평균값을 가져옵니다. 与 avgIf * ``meanIf(region == 'West', sales)``
___
### <code>min</code>相同 
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
열의 최소값을 가져옵니다. * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
基于条件，获取列 * ``minIf(region == 'West', sales)``
___
### <code>skewness</code>的最小值 
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``skewness(sales)``
___
### <code>skewnessIf</code>的偏斜度 
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``skewnessIf(region == 'West', sales)``
___
### <code>stddev</code>的偏斜度 
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``stdDev(sales)``
___
### <code>stddevIf</code>的标准偏差 
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``stddevIf(region == 'West', sales)``
___
### <code>stddevPopulation</code>的标准偏差 
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>的总体标准偏差 
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``stddevPopulationIf(region == 'West', sales)``
___
### <code>stddevSample</code>的总体标准偏差 
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>的样本标准偏差 
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``stddevSampleIf(region == 'West', sales)``
___
### <code>sum</code>的样本标准偏差 
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
获取数值列 * ``sum(col)``
___
### <code>sumDistinct</code>的聚合总和 
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
获取数值列 * ``sumDistinct(col)``
___
### <code>sumDistinctIf</code>的非重复值的聚合总和 
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 条件可以基于任何列 * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 条件可以基于任何列 * ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``variance(sales)``
___
### <code>varianceIf</code>的方差 
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``varianceIf(region == 'West', sales)``
___
### <code>variancePopulation</code>的方差 
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>的总体方差 
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``variancePopulationIf(region == 'West', sales)``
___
### <code>varianceSample</code>的总体方差 
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
获取列 * ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>的无偏差方差 
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
基于条件，获取列 * ``varianceSampleIf(region == 'West', sales)``
## 窗口函数的无偏差方差。以下函数仅可用于窗口转换 ___
### <code>cumeDist</code>中 
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 함수는 파티션의 모든 값을 기준으로 값의 위치를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행 수를 창 파티션의 총 행 수로 나눈 값입니다. 排序中的任何并列值都将计算为相同的位置。
* ``cumeDist()``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
값 그룹에 있는 값의 순위를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성하지 않습니다. 即使数据未排序并在值中查找更改 * ``denseRank(salesQtr, salesAmt)``
___
### <code>lag</code>，密集排名仍有效 
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
현재 행보다 n개 행 전에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 第二个参数是要返回的行数，默认值为1。 如果没有多行，则返回值为 null 的值，除非指定默认值 * ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
현재 행보다 n개 행 뒤에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 第二个参数是要向前看的行数，默认值为1。 如果没有多行，则返回值为 null 的值，除非指定默认值 * ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
NTile 함수는 각 창 파티션의 행을 1부터 `n`개 이내의 `n` 버킷 수로 나눕니다. 버킷 값 차이는 최대 1 이내입니다. 파티션의 행 수가 버킷 수로 균일하게 나누어 떨어지지 않으면 나머지 값은 첫 번째 버킷부터 시작해서 하나씩 분산됩니다. NTile 函数可用于计算 tertiles、四分位数、十分位数和其他常见汇总统计信息。 函数在初始化期间计算两个变量：常规 bucket 的大小将额外添加一行。 这两个变量都以当前分区的大小为基础。 계산 프로세스 동안 이 함수는 현재 행 번호, 현재 버킷 수 및 버킷이 변경되는 행 번호(bucketThreshold)를 추적합니다. 현재 행 번호가 버킷 임계값에 도달하면 버킷 값이 1씩 커지고, 임계값은 버킷 크기만큼 커집니다(현재 버킷이 채워질 경우 하나씩 추가).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
값 그룹에 있는 값의 순위를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성합니다. 即使数据未排序并在值中查找更改 * ``rank(salesQtr, salesAmt)``
___
### <code>rowNumber</code>，排名仍有效 
<code><b>rowNumber() => integer</b></code><br/><br/>
1부터 시작하는 창에서 행에 대해 순차적인 번호 매기기를 할당합니다. * ``rowNumber()``

## <a name="next-steps"></a>다음 단계

[了解如何使用表达式生成器](concepts-data-flow-expression-builder.md)。
