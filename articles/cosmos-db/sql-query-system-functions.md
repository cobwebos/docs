---
title: 系统函数
description: 了解 Azure Cosmos DB 中的 SQL 系统函数。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b0e9c751d46f805af75196da464a39783c95ae6a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619991"
---
# <a name="system-functions"></a>系统函数

 Cosmos DB 提供多个内置 SQL 函数。 内置函数的类别如下所示。  
  
|函数|描述|  
|--------------|-----------------|  
|[数学函数](#mathematical-functions)|每个数学函数均执行一个计算，通常基于作为参数提供的输出值，并返回数值。|  
|[类型检查函数](#type-checking-functions)|类型检查函数允许检查 SQL 查询内表达式的类型。|  
|[字符串函数](#string-functions)|该字符串函数对字符串输入值执行操作，并返回字符串、数值或布尔值。|  
|[数组函数](#array-functions)|该数组函数对数组输入值执行操作，并返回数值、布尔值或数组值。|
|[日期和时间函数](#date-time-functions)|使用日期和时间函数可以获取采用以下两种格式的当前 UTC 日期和时间：一个时间戳，其值为以毫秒为单位的 Unix 纪元；一个符合 ISO 8601 格式的字符串。|
|[空间函数](#spatial-functions)|该空间函数对控件对象输入值执行操作，并返回数值或布尔值。|  

下面列出了每个类别中的函数:

| 函数组 | 操作 |
|---------|----------|
| 数学函数 | ABS、天花板、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、方形、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、度数、PI、RADIANS、RAND、SIN、TAN |
| 类型检查函数 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED 和 IS_PRIMITIVE |
| 字符串函数 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING 和 UPPER |
| 数组函数 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH 和 ARRAY_SLICE |
| 日期和时间函数 | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| 空间函数 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID 和 ST_ISVALIDDETAILED |

如果当前正在使用内置函数可用的用户定义函数 (UDF), 则相应的内置函数将更快运行, 更有效率。

Cosmos DB 函数与 ANSI SQL 函数之间的主要差别在于，Cosmos DB 函数能够很好地处理无架构数据和混合架构数据。 例如，如果某个属性缺失或包含类似于 `unknown` 的非数字值，则会跳过该项，而不是返回错误。

##  <a name="mathematical-functions"></a> 数学函数  

每个数学函数均执行一个计算，基于作为参数提供的输出值，并返回数值。

可以运行以下示例所示的查询：

```sql
    SELECT VALUE ABS(-4)
```

结果为：

```json
    [4]
```

以下是受支持的内置数学函数表。
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[RAND](#bk_rand)|
|[SIN](#bk_sin)|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|
|[SIGN](#bk_sign)|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 返回指定数值表达式的绝对（正）值。  
  
 **语法**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例显示了对三个不同数字使用 ABS 函数的结果。  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 结果集如下。  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 返回角度（弧度），其余弦是指定的数值表达式；也被称为反余弦。  
  
 **语法**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回 -1 的反余弦值。  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 结果集如下。  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 返回角度（弧度），其正弦是指定的数值表达式。 也被称为反正弦。  
  
 **语法**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回  -1 的反正弦值。  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 结果集如下。  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 返回角度（弧度），其正切是指定的数值表达式。 这也被称为反正切。  
  
 **语法**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回指定值的反正切值。  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 结果集如下。  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 返回 y/x 的反正切函数的主值（以弧度表示）。  
  
 **语法**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例可计算指定的 x 分量和 y 分量的 ATN2 值。  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 结果集如下。  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 返回大于或等于指定数值表达式的最小整数值。  
  
 **语法**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例显示了 CEILING 函数的正数、负数和零值。  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 结果集如下。  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 返回指定表达式中指定角度的三角余弦（弧度）。  
  
 **语法**  
  
```  
COS(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例可计算指定角的余弦值。  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 结果集如下。  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 返回指定数值表达式中指定角度的三角余切。  
  
 **语法**  
  
```  
COT(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例可计算指定角的余切值。  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 结果集如下。  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 返回指定角度（弧度）的相应角度（度）。  
  
 **语法**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回 PI/2 弧度角的度数。  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 结果集如下。  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 返回小于或等于指定数值表达式的最大整数。  
  
 **语法**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例显示了 FLOOR 函数的正数、负数和零值。  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 结果集如下。  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 返回指定数值表达式的指数值。  
  
 **语法**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回一个数值表达式。  
  
  **注释**  
  
  常数 e  (2.718281…) 是自然对数的底数。  
  
  某个数为指数表示常数 e 自乘的次数为该数。 例如 EXP(1.0) = e^1.0 = 2.71828182845905，EXP(10) = e^10 = 22026.4657948067。  
  
  某个数的自然对数的指数就是该数本身：EXP (LOG (n)) = n。 并且某个数的指数的自然对数也是该数本身：LOG (EXP (n)) = n。  
  
  **示例**  
  
  以下示例声明一个变量并返回到指定变量 (10) 的指数值。  
  
```  
SELECT EXP(10) AS exp  
```  
  
 结果集如下。  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 以下示例将返回 20 的自然对数的指数值以及 20 的指数的自然对数。 由于这些函数互为反函数，因此在这两种情况下，用于浮点数学舍入的返回值均为 20。  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 结果集如下。  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 返回指定数值表达式的自然对数。  
  
 **语法**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   为数值表达式。  
  
- `base`  
  
   设置对数底数的可选数值参数。  
  
  返回类型  
  
  返回一个数值表达式。  
  
  **备注**  
  
  默认情况下，LOG() 返回自然对数。 可以使用可选底数参数将对数的底数改为另一个值。  
  
  自然对数是以 e 为底数的对数，其中 e 是一个约等于2.718281828 的无理数常数。  
  
  某个数的指数的自然对数也是该数本身：LOG( EXP( n ) ) = n。 并且某个数的自然对数的指数就是该数本身：EXP( LOG( n ) ) = n。  
  
  **示例**  
  
  以下示例声明一个变量并返回指定变量 (10) 的对数值。  
  
```  
SELECT LOG(10) AS log  
```  
  
 结果集如下。  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 以下示例可计算某个数的指数的对数值。  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 结果集如下。  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 返回指定数值表达式中以 10 为底数的对数。  
  
 **语法**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回一个数值表达式。  
  
  **注释**  
  
  LOG10 和 POWER 函数互为反函数。 例如，10 ^ LOG10(n) = n。  
  
  **示例**  
  
  以下示例声明一个变量并返回到指定变量 (100) 的 LOG10 值。  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 结果集如下。  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 返回 PI 的常数值。  
  
 **语法**  
  
```  
PI ()  
```  
   
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回 PI 值。  
  
```  
SELECT PI() AS pi 
```  
  
 结果集如下。  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 返回指定表达式相对指定幂的值。  
  
 **语法**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   为数值表达式。  
  
- `y`  
  
   为 `numeric_expression` 自乘得到的幂。  
  
  **返回类型**  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例演示了某个数自乘 3 次的幂（该数的立方）。  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 结果集如下。  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 返回输入的数值表达式（度）的弧度。  
  
 **语法**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将几个角作为输入，然后返回其对应的弧度值。  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  结果集如下。  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 返回一个数值，四舍五入到最接近的整数值。  
  
 **语法**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回一个数值表达式。  
  
  **注释**
  
  执行的四舍五入运算遵循远离零的中点四舍五入。 如果输入是正好介于两个整数之间的数值表达式，则结果将是离零最近的整数值。  
  
  |<numeric_expression>|已四舍五入|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
  **示例**  
  
  以下示例将以下的正数和负数舍入到最接近的整数。  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  结果集如下。  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

#### <a name="bk_rand"></a>RAND
 从 [0, 1) 返回随机生成的数值。
 
 **语法**  
  
```  
RAND ()  
```  

  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  下面的示例返回一个随机生成的数值。  
  
```  
SELECT RAND() AS rand 
```  
  
 结果集如下。  
  
```  
[{"rand": 0.87860053195618093}]  
``` 

####  <a name="bk_sign"></a> SIGN  
 返回指定数值表达式的正数 (+1)、零 (0) 或负数 (-1)。  
  
 **语法**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回数字 -2 到 2 的符号函数值。  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 结果集如下。  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 返回指定表达式中指定角度的三角正弦（弧度）。  
  
 **语法**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例可计算指定角的正弦值。  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 结果集如下。  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 返回指定数值的平方根。  
  
 **语法**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回数字 1-3 的平方根。  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 结果集如下。  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 返回指定数字值的平方。  
  
 **语法**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回数字 1-3 的平方。  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 结果集如下。  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 返回指定表达式中指定角度（弧度）的正切值。  
  
 **语法**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例可计算 PI()/2 的正切值。  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 结果集如下。  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 返回一个数值，截断到最接近的整数值。  
  
 **语法**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **参数**  
  
- `numeric_expression`  
  
   是一个数值表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将以下的正数和负数截断为最接近的整数。  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 结果集如下。  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>类型检查函数

使用类型检查函数可以检查 SQL 查询中表达式的类型。 当项中的属性可变或未知时，可以使用类型检查函数即时确定这些属性的类型。 下面是受支持的内置类型检查函数表:

以下函数支持对输入值进行类型检查，并且每个都会返回一个布尔值。  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 返回一个布尔值，指示指定表达式类型是否为数组。  
  
 **语法**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例使用 IS_ARRAY 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 结果集如下。  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 返回一个布尔值，指示指定表达式的类型是否为布尔表达式。  
  
 **语法**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例使用 IS_BOOL 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 结果集如下。  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 返回一个布尔，它指示属性是否已经分配了值。  
  
 **语法**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例检查了指定的 JSON 文档内是否存在属性。 由于存在 "a"，第一个会返回 true，但由于不存在 "b"，第二个会返回 false。  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 结果集如下。  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 返回一个布尔值，指示指定表达式的类型是否为 null。  
  
 **语法**  
  
```  
IS_NULL(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例使用 IS_NULL 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 结果集如下。  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 返回一个布尔值，指示指定表达式的类型是否为数字。  
  
 **语法**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例使用 IS_NULL 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 结果集如下。  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 返回一个布尔值，指示指定表达式的类型是否为 JSON 对象。  
  
 **语法**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例使用 IS_OBJECT 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 结果集如下。  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 返回一个布尔值，指示指定表达式的类型是否为一个（字符串、布尔、数值或 null）。  
  
 **语法**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例使用 IS_PRIMITIVE 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 结果集如下。  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 返回一个布尔值，指示指定表达式的类型是否为字符串。  
  
 **语法**  
  
```  
IS_STRING(<expression>)  
```  
  
 **参数**  
  
- `expression`  
  
   为任何有效的表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例使用 IS_STRING 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 结果集如下。  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>字符串函数

下面的标量函数对字符串输入值执行操作, 并返回字符串、数值或布尔值:
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 返回一个字符串，该字符串是连接两个或多个字符串值的结果。  
  
 **语法**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例将返回指定值的串联字符串。  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 结果集如下。  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串表达式。  
  
 **语法**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例检查了 "abc" 是否包含 "ab" 且包含 "d"。  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 结果集如下。  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。  
  
 **语法**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例将返回以 "b" 和 "bc" 结尾的 "abc"。  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 结果集如下。  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 返回第一个指定的字符串表达式中第一次出现第二个字符串表达式的起始位置，如果未找到字符串，则返回 -1。  
  
 **语法**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例将返回 "abc" 内不同子字符串的索引。  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 结果集如下。  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 返回具有指定字符数的字符串的左侧部分。  
  
 **语法**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
- `num_expr`  
  
   为任何有效的数值表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例将返回不同长度值的 "abc" 的左侧部分。  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 下面是结果集。  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 返回指定字符串表达式的字符数。  
  
 **语法**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例将返回字符串的长度。  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 结果集如下。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 返回在将大写字符数据转换为小写后的字符串表达式。  
  
 **语法**  
  
```  
LOWER(<str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 LOWER。  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 结果集如下。  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 返回删除前导空格后的字符串表达式。  
  
 **语法**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 LTRIM。  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 结果集如下。  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 将出现的所有指定字符串值替换为另一个字符串值。  
  
 **语法**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 REPLACE。  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 结果集如下。  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 将一个字符串值重复指定的次数。  
  
 **语法**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
- `num_expr`  
  
   为任何有效的数值表达式。 如果 num_expr 为负或非有限，则结果未定义。

  > [!NOTE]
  > 结果的最大长度为 10,000 个字符，即 (length(str_expr)  *  num_expr) <= 10,000。
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 REPLICATE。  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 结果集如下。  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 返回字符串值的逆序排序形式。  
  
 **语法**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 REVERSE。  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 结果集如下。  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 返回具有指定字符数的字符串的右侧部分。  
  
 **语法**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
- `num_expr`  
  
   为任何有效的数值表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例将返回不同长度值的 "abc" 的右侧部分。  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 结果集如下。  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 在删除尾随空格后，返回字符串表达式。  
  
 **语法**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 RTRIM。  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 结果集如下。  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。  
  
 **语法**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例检查了字符串 "abc" 是否以 "b" 和 "a" 开头。  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 结果集如下。  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 返回已转换为数组的表达式。 如果表达式无法转换，则返回未定义的表达式。  
  
 **语法**  
  
```  
StringToArray(<expr>)  
```  
  
 **参数**  
  
- `expr`  
  
   是否会将任何有效的标量表达式作为 JSON 数组表达式来计算？ 请注意，嵌套字符串值必须使用双引号编写，否则无效。 有关 JSON 格式的详细信息，请参阅 [json.org](https://json.org/)
  
  返回类型  
  
  返回一个数组表达式或未定义的表达式。  
  
  **示例**  
  
  以下示例演示 StringToArray 在不同类型中的行为方式。 
  
 下面是输入有效的示例。

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

结果集如下。

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

下面是输入无效的示例。 
   
 在数组中使用单引号不是有效的 JSON。
即使它们在查询中有效，系统也不会将其解析为有效数组。 必须将数组字符串中的字符串转义为 "[\\"\\"]"，否则其引号必须为单个 '[""]'。

```
SELECT
    StringToArray("['5','6','7']")
```

结果集如下。

```
[{}]
```

下面是输入无效的示例。
   
 传递的表达式将会解析为 JSON 数组；下面的示例不会计算为类型数组，因此返回未定义。
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

结果集如下。

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 返回已转换为布尔值的表达式。 如果表达式无法转换，则返回未定义的表达式。  
  
 **语法**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **参数**  
  
- `expr`  
  
   是否会将任何有效的标量表达式作为布尔表达式来计算？  
  
  返回类型  
  
  返回一个布尔表达式或未定义的表达式。  
  
  **示例**  
  
  以下示例演示 StringToBoolean 在不同类型中的行为方式。 
 
 下面是输入有效的示例。

只能在 "true"/"false" 之前或之后使用空格。

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 结果集如下。  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

下面是输入无效的示例。

 布尔值区分大小写，必须全用小写字符（即 "true" 和 "false"）来表示。

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

结果集如下。  
  
```  
[{}]
``` 

传递的表达式将会解析为布尔表达式；以下输入不会计算为布尔类型，因此会返回未定义。

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

结果集如下。  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 返回已转换为 Null 的表达式。 如果表达式无法转换，则返回未定义的表达式。  
  
 **语法**  
  
```  
StringToNull(<expr>)  
```  
  
 **参数**  
  
- `expr`  
  
   是否会将任何有效的标量表达式作为 Null 表达式来计算？
  
  返回类型  
  
  返回一个 Null 表达式或未定义的表达式。  
  
  **示例**  
  
  以下示例演示 StringToNull 在不同类型中的行为方式。 

下面是输入有效的示例。

 只能在 "null" 之前或之后使用空格。

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 结果集如下。  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

下面是输入无效的示例。

Null 值区分大小写，必须全用小写字符（即 "null"）来表示。

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 结果集如下。  
  
```  
[{}]
```  

传递的表达式将会解析为 null 表达式；以下输入不会计算为 null 类型，因此会返回未定义。

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 结果集如下。  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 返回已转换为数字值的表达式。 如果表达式无法转换，则返回未定义的表达式。  
  
 **语法**  
  
```  
StringToNumber(<expr>)  
```  
  
 **参数**  
  
- `expr`  
  
   是否会将任何有效的标量表达式作为 JSON 数字表达式来计算？ JSON 中的数字必须是整数或浮点数。 有关 JSON 格式的详细信息，请参阅 [json.org](https://json.org/)  
  
  返回类型  
  
  返回一个数字表达式或未定义的表达式。  
  
  **示例**  
  
  以下示例演示 StringToNumber 在不同类型中的行为方式。 

只能在 Number 之前或之后使用空格。

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 结果集如下。  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

在 JSON 中，有效的 Number 必须是整数或浮点数。

```  
SELECT   
    StringToNumber("0xF")
```  
  
 结果集如下。  
  
```  
{{}}
```  

传递的表达式将会解析为 Number 表达式；以下输入不会计算为 Number 类型，因此会返回未定义。 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 结果集如下。  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 返回已转换为对象的表达式。 如果表达式无法转换，则返回未定义的表达式。  
  
 **语法**  
  
```  
StringToObject(<expr>)  
```  
  
 **参数**  
  
- `expr`  
  
   是否会将任何有效的标量表达式作为 JSON 对象表达式来计算？ 请注意，嵌套字符串值必须使用双引号编写，否则无效。 有关 JSON 格式的详细信息，请参阅 [json.org](https://json.org/)  
  
  返回类型  
  
  返回一个对象表达式或未定义的表达式。  
  
  **示例**  
  
  以下示例演示 StringToObject 在不同类型中的行为方式。 
  
 下面是输入有效的示例。

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

结果集如下。

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 下面是输入无效的示例。
即使它们在查询中有效，系统也不会将其解析为有效对象。 必须将对象字符串中的字符串转义为 "{\\"a\\":\\"str\\"}"，否则其引号必须为单个 '{"a": "str"}'。

属性名称的单引号不是有效的 JSON。

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

结果集如下。

```  
[{}]
```  

没有引号的属性名称不是有效的 JSON。

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

结果集如下。

```  
[{}]
``` 

下面是输入无效的示例。

 传递的表达式将会解析为 JSON 对象；以下输入不会计算为对象类型，因此会返回未定义。

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 结果集如下。

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 返回字符串表达式的部分内容，该内容起于指定字符从零开始的位置，继续到指定长度或字符串结尾。  
  
 **语法**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
- `num_expr`  
  
   是表示开始和结束字符的任何有效数字表达式。    
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例将返回 "abc" 中从位置 1 开始且长度为 1 个字符的子字符串。  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 结果集如下。  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 返回标量表达式的字符串表示形式。 
  
 **语法**  
  
```  
ToString(<expr>)
```  
  
 **参数**  
  
- `expr`  
  
   为任何有效的标量表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例演示 ToString 在不同类型中的行为方式。   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 结果集如下。  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 给定以下输入：
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 以下示例演示 ToString 如何与其他字符串函数（如 CONCAT）一起使用。   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

结果集如下。  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
给定以下输入。
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
以下示例演示 ToString 如何与其他字符串函数（如 REPLACE）一起使用。   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
结果集如下。  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 返回删除前导空格和尾随空格后的字符串表达式。  
  
 **语法**  
  
```  
TRIM(<str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 TRIM。  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 结果集如下。  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 返回在将小写字符数据转换为大写后的字符串表达式。  
  
 **语法**  
  
```  
UPPER(<str_expr>)  
```  
  
 **参数**  
  
- `str_expr`  
  
   为任何有效的字符串表达式。  
  
  返回类型  
  
  返回字符串表达式。  
  
  **示例**  
  
  以下示例介绍了如何在查询中使用 UPPER。  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 结果集如下。  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>数组函数

下面的标量函数对数组输入值执行运算, 并返回数值、布尔值或数组值:
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 返回一个数组，该数组是连接两个或更多数组值的结果。  
  
 **语法**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **参数**  
  
- `arr_expr`  
  
   为任何有效的数组表达式。  
  
  返回类型  
  
  返回数组表达式。  
  
  **示例**  
  
  以下示例介绍了如何连接两个数组。  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 结果集如下。  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
返回一个布尔，它指示数组是否包含指定的值。 可以通过在命令中使用布尔表达式来检查对象的部分匹配或完全匹配。 

**语法**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **参数**  
  
- `arr_expr`  
  
   为任何有效的数组表达式。  
  
- `expr`  
  
   为任何有效的表达式。  

- `bool_expr`  
  
   为任何布尔表达式。 如果将其设置为“true”，并且指定的搜索值是对象，则该命令将检查部分匹配（搜索对象是其中一个对象的子集）。 如果将其设置为“false”，则该命令将检查数组中所有对象的完全匹配。 如果未指定，默认值为“false”。 
  
  返回类型  
  
  返回一个布尔值。  
  
  **示例**  
  
  以下示例介绍了如何使用 ARRAY_CONTAINS 检查数组内的成员资格。  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 结果集如下。  
  
```  
[{"b1": true, "b2": false}]  
```  

以下示例介绍了如何使用 ARRAY_CONTAINS 检查数组内是否存在 JSON 字符串的部分匹配字符串。  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 结果集如下。  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 返回指定数组表达式的元素数。  
  
 **语法**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **参数**  
  
- `arr_expr`  
  
   为任何有效的数组表达式。  
  
  返回类型  
  
  返回数值表达式。  
  
  **示例**  
  
  以下示例介绍了如何使用 ARRAY_LENGTH 获得数组的长度。  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 结果集如下。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 返回部分数组表达式。
  
 **语法**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **参数**  
  
- `arr_expr`  
  
   为任何有效的数组表达式。  
  
- `num_expr`  
  
   用于开始数组的从零开始的数字索引。 负值可用于指定相对于数组最后一个元素的起始索引，即 -1 引用数组中最后一个元素。  

- `num_expr`  

   结果数组中的最大元素数。    

  返回类型  
  
  返回数组表达式。  
  
  **示例**  
  
  以下示例展示了如何使用 ARRAY_SLICE 获取数组的不同切片。  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 结果集如下。  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>日期和时间函数

使用以下标量函数可以获取采用以下两种格式的当前 UTC 日期和时间：一个时间戳，其值为以毫秒为单位的 Unix 纪元；一个符合 ISO 8601 格式的字符串。 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a>GETCURRENTDATETIME
 以 ISO 8601 字符串形式返回当前 UTC 日期和时间。
  
 **语法**
  
```
GETCURRENTDATETIME ()
```
  
  返回类型
  
  以 ISO 8601 字符串值形式返回当前 UTC 日期和时间。 

  此值以 YYYY-MM-DDThh:mm:ss.sssZ 格式表示，其中：
  
  |||
  |-|-|
  |YYYY|四位数的年份|
  |MM|两位数的月份（01 = 1 月，依此类推。）|
  |DD|两位数的月份日期（01 到 31）|
  |T|时间元素开头的符号|
  |hh|两位数小时（00 到 23）|
  |mm|两位数分钟（00 到 59）|
  |ss|两位数秒（00 到 59）|
  |.sss|三位数的秒小数部分|
  |Z|UTC（协调世界时）指示符||
  
  有关 ISO 8601 格式的更多详细信息，请参阅 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **注释**

  GETCURRENTDATETIME 是不确定性函数。 
  
  返回的结果采用 UTC（协调世界时）格式。

  **示例**  
  
  以下示例演示如何使用 GetCurrentDateTime 内置函数获取当前 UTC 日期时间。
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 下面是示例结果集。
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a>GETCURRENTTIMESTAMP
 返回自 1970 年 1 月 1 日星期四 00:00:00 开始消逝的毫秒数。 
  
 **语法**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  返回类型  
  
  返回一个数字值，表示自 Unix 纪元开始消逝的秒数，即自 1970 年 1 月 1 日星期四 00:00:00 开始消逝的毫秒数。

  **注释**

  GETCURRENTTIMESTAMP 是不确定性函数。
  
  返回的结果采用 UTC（协调世界时）格式。

  **示例**  
  
  以下示例演示如何使用 GetCurrentTimestamp 内置函数获取当前时间戳。
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 下面是示例结果集。
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>空间函数

Cosmos DB 支持以下用于查询地理空间的开放地理空间信息联盟 (OGC) 内置函数。 以下标量函数对标量对象输入值执行操作，并返回数值或布尔值。  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 返回两个 GeoJSON 点、多边形或 LineString 表达式之间的距离。  
  
 **语法**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **参数**  
  
- `spatial_expr`  
  
   为任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
  
  返回类型  
  
  返回包含距离的数值表达式。 默认参考系统以米为单位表示。  
  
  **示例**  
  
  以下示例介绍了如何使用 ST_DISTANCE 内置函数返回在指定位置 30 km 内的所有家族文档。 .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 结果集如下。  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 返回一个布尔表达式，指示第一个参数中指定的 GeoJSON 对象（点、多边形或 LineString）是否在第二个参数中的 GeoJSON（点、多边形或 LineString）内。  
  
 **语法**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **参数**  
  
- `spatial_expr`  
  
   是任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
 
- `spatial_expr`  
  
   为任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
  
  返回类型  
  
  返回一个布尔值。  
  
  **示例**  
  
  以下示例介绍了如何使用 ST_WITHIN 查找多边形内的所有家族文档。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 结果集如下。  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 返回一个布尔表达式，指示第一个参数中指定的 GeoJSON 对象（点、多边形或 LineString）是否与第二个参数中的 GeoJSON（点、多边形或 LineString）相交。  
  
 **语法**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **参数**  
  
- `spatial_expr`  
  
   是任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
 
- `spatial_expr`  
  
   为任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
  
  返回类型  
  
  返回一个布尔值。  
  
  **示例**  
  
  以下示例介绍了如何查找与给定多边形相交的所有区域。  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 结果集如下。  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 返回一个布尔值，指示指定的 GeoJSON 点、多边形或 LineString 表达式是否有效。  
  
 **语法**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **参数**
  
- `spatial_expr`  
  
   为任何有效的 GeoJSON 点、多边形或 LineString 表达式。  
  
  返回类型  
  
  返回一个布尔表达式。  
  
  **示例**  
  
  以下示例介绍了如何使用 ST_VALID 检查点是否有效。  
  
  例如，由于此点具有一个不在有效值范围 [-90，90] 内的纬度值，因此查询返回 false。  
  
  对于多边形，GeoJSON 规范要求提供的最后一个坐标対应该与第一个坐标对相同，才能创建一个闭合形状。 多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 结果集如下。  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 如果指定的 GeoJSON 点、多边形或 LineString 表达式有效，则返回包含布尔值的 JSON 值；如果无效，则额外加上作为字符串值的原因。  
  
 **语法**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **参数**  
  
- `spatial_expr`  
  
   任何有效的 GeoJSON 点或多边形表达式。  
  
  返回类型  
  
  如果指定的 GeoJSON 点或多边形表达式有效，则返回包含布尔值的 JSON 值；如果无效，则额外添加原因作为字符串值。  
  
  **示例**  
  
  以下示例介绍了如何使用 ST_ISVALIDDETAILED 检查有效性（附带详细信息）。  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 结果集如下。  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [Udf](sql-query-udfs.md)
- [集中](sql-query-aggregates.md)
