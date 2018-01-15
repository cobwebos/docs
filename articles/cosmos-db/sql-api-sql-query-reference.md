---
title: "Azure Cosmos DB：SQL 语法查询参考 | Microsoft Docs"
description: "Azure Cosmos DB SQL 查询语言参考文档。"
services: cosmos-db
author: LalithaMV
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: laviswa
ms.openlocfilehash: 968f9e4d643228e02a1d09aae1b6ce82d6775f25
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2018
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Azure Cosmos DB SQL 语法参考

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB 支持使用分层 JSON 文档中的语法等熟悉的 SQL（结构化查询语言）来查询文档，无需显示架构或创建辅助索引。 本主题提供 SQL 查询语言（与 SQL API 帐户兼容）的参考文档。

有关 SQL 查询语言的演练，请参阅 [Azure Cosmos DB 的 SQL 查询](sql-api-sql-query.md)。  
  
我们同时邀请你访问[查询板块](http://www.documentdb.com/sql/demo)，你可以在其中尝试 Azure Cosmos DB，并针对数据集运行 SQL 查询。  
  
## <a name="select-query"></a>SELECT 查询  
从数据库中检索 JSON 文档。 支持表达式计算、投影、筛选和联接。  用于描述 SELECT 语句的约定在“语法约定”部分以表格形式列出。  
  
**语法**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **备注**  
  
 有关每个子句的详细信息，请参阅以下部分。  
  
-   [SELECT 子句](#bk_select_query)  
  
-   [FROM 子句](#bk_from_clause)  
  
-   [WHERE 子句](#bk_where_clause)  
  
-   [ORDER BY 子句](#bk_orderby_clause)  
  
SELECT 语句中的子句必须按照以上所示进行排序。 可以省略任一可选子句。 但是当使用可选子句时，它们必须按正确的顺序显示。  
  
**SELECT 语句的逻辑处理顺序**  
  
处理子句的顺序是：  

1.  [FROM 子句](#bk_from_clause)  
2.  [WHERE 子句](#bk_where_clause)  
3.  [ORDER BY 子句](#bk_orderby_clause)  
4.  [SELECT 子句](#bk_select_query)  

请注意，此顺序与语法中出现的顺序不同。 采用此排序是为了使已处理子句引入的所有新符号可见，并且可用于后来处理的字句中。 例如，在 WHERE 和 SELECT 子句中可访问在 FROM 子句中声明的别名。  

空格字符和注释  

不属于带引号的字符串或带引号的标识符中的所有空格字符也不属于语言语法的一部分，在解析过程中将被忽略。  

查询语言支持 T-SQL 样式注释，如  

-   SQL 语句`-- comment text [newline]`  

虽然空格字符和注释在语法中没有任何意义，但它们必须用于分隔令牌。 例如：`-1e5` 是单个数字令牌，而 `: – 1 e5` 是减号令牌，后跟数字 1 和标识符 e5。  

##  <a name="bk_select_query"></a> SELECT 子句  
SELECT 语句中的子句必须按照以上所示进行排序。 可以省略任一可选子句。 但是当使用可选子句时，它们必须按正确的顺序显示。  

**语法**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **参数**  
  
 `<select_specification>`  
  
 为结果集选择的属性或值。  
  
 `'*'`  
  
指定在不进行任何更改的情况下检索该值。 具体来说，如果处理的值是一个对象，将检索所有属性。  
  
 `<object_property_list>`  
  
指定要检索的属性列表。 每个返回值将是一个具有指定属性的对象。  
  
`VALUE`  
  
指定应检索 JSON 值而不是完整的 JSON 对象。 与 `<property_list>` 不同的是，它不会将投影值包装在对象中。  
  
`<scalar_expression>`  
  
表示要计算的值的表达式。 请参阅[标量表达式](#bk_scalar_expressions)部分，了解详细信息。  
  
**备注**  
  
只有当 FROM 子句已声明一个别名时，`SELECT *` 语法才有效。 `SELECT *` 提供一个标识投影，在不需要投影的情况下会有所帮助。 只有当指定了 FROM 子句，并且该字句只引入单个输入源时，SELECT * 才有效。  
  
请注意，`SELECT <select_list>` 和 `SELECT *` 是“语法糖”，可另外使用简单的 SELECT 语句表示，如下所示。  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     等效于：  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     等效于：  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
另请参阅  
  
[标量表达式](#bk_scalar_expressions)  
[SELECT 子句](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM 子句  
指定源或联接源。 FROM 子句是可选项。 如果未指定，其他子句仍将继续执行，如同 FROM 子句提供了单个文档。  
  
**语法**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**参数**  
  
`<from_source>`  
  
指定一个具有或不具有别名的数据源。 如果未指定别名，将从 `<collection_expression>` 中使用以下规则推断：  
  
-   如果表达式为 collection_name，那么 collection_name 将用作别名。  
  
-   如果表达式为 `<collection_expression>`，其次是 property_name，那么 property_name 将用作别名。 如果表达式为 collection_name，那么 collection_name 将用作别名。  
  
AS `input_alias`  
  
指定 `input_alias` 为基础集合表达式返回的一组值。  
 
`input_alias` IN  
  
指定 `input_alias` 应表示通过遍历由基础集合表达式返回的每个数组的所有数组元素获得的一组值。 基础集合表达式返回的非数组的任何值都将被忽略。  
  
`<collection_expression>`  
  
指定要用于检索文档的集合表达式。  
  
`ROOT`  
  
指定应从当前连接的默认集合中检索文档。  
  
`collection_name`  
  
指定应从当前提供的集合中检索文档。 集合的名称必须匹配当前连接到的集合名称。  
  
`input_alias`  
  
指定应从提供的别名定义的其他源中检索文档。  
  
`<collection_expression> '.' property_`  
  
指定应通过访问由指定集合表达式检索的所有文档的 `property_name` 属性或 array_index 数组元素来检索文档。  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
指定应通过访问由指定集合表达式检索的所有文档的 `property_name` 属性或 array_index 数组元素来检索文档。  
  
**备注**  
  
`<from_source>(`) 中提供或推断的所有别名必须唯一。 语法 `<collection_expression>.`property_name 等同于 `<collection_expression>' ['"property_name"']'`。 但是，如果属性名称包含非标识符字符，则可以使用后一种语法。  
  
未命中属性、未命中数组元素、未定义值处理  
  
如果某个集合表达式访问属性或数组元素，但该值不存在，将忽略该值，且不会做进一步处理。  
  
**集合表达式上下文范围**  
  
集合表达式的范围可能为集合或文档：  
  
-   如果集合表达式的基础源是 ROOT 或 `collection_name`，则表达式的范围是集合。 此类表达式表示从集合中直接检索的一组文档，且不依赖于其他集合表达式的处理。  
  
-   如果集合表达式的基础源是之前在查询中引入的 `input_alias`，则表达式的范围是文档。 此类表达式表示通过计算每个文档范围内的集合表达式获得的一组文档，该文档属于与别名集合相关联的集。  结果集为多个集的合并，通过计算基础集中每个文档的集合表达式获得。  
  
**联接**  
  
在当前发布的版本中，Azure Cosmos DB 支持内联。 其他联接功能即将发布。

内联会导致加入联接的集产生完整叉积。 N 向联接的结果是获得一组 N-元素元组，其中元组中的每个值与参与联接的别名集相关联，并且可以通过引用其他子句中的这些别名来访问。  
  
联接的计算取决于参与集的上下文范围：  
  
-  集合集 A 和集合范围的集 B 之间的联接，会导致集 A 和集 B 的所有元素产生叉积。
  
-   集 A 和文档范围的集 B 之间的联接，通过对集 A 中每个文档计算文档范围的集 B 来获得所有集的合并。  
  
 在当前发布的版本中，查询处理器支持一个集合范围的表达式的最大值。  
  
**联接示例：**  
  
请看以下 FROM 子句：`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 让每个源定义 `input_alias1, input_alias2, …, input_aliasN`。 此 FROM 子句将返回一组 N 元组（带有 N 个值的元组）。 每个元组拥有通过对它们相应的集遍历所有集合别名所产生的值。  
  
JOIN 示例 1，具有 2 个源：  
  
- 让 `<from_source1>` 的范围为集合，并表示集 {A, B, C}。  
  
- 让 `<from_source2>` 作为文档范围的引用 input_alias1，表示的集如下所示：  
  
    若 `input_alias1 = A,`，表示集 {1, 2}  
  
    若 `input_alias1 = B,`，表示集 {3}  
  
    若 `input_alias1 = C,`，表示集 {4, 5}  
  
- FROM 子句 `<from_source1> JOIN <from_source2>` 将产生以下元组：  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*JOIN 示例 2，具有 3 个源：*  
  
- 让 `<from_source1>` 的范围为集合，并表示集 {A, B, C}。  
  
- 让 `<from_source2>` 作为文档范围的引用 `input_alias1`，表示的集如下所示：  
  
    若 `input_alias1 = A,`，表示集 {1, 2}  
  
    若 `input_alias1 = B,`，表示集 {3}  
  
    若 `input_alias1 = C,`，表示集 {4, 5}  
  
- 让 `<from_source3>` 作为文档范围的引用 `input_alias2`，表示的集如下所示：  
  
    若 `input_alias2 = 1,`，表示集 {100, 200}  
  
    若 `input_alias2 = 3,`，表示集 {300}  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 将产生以下元组：  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> `input_alias1` 和 `input_alias2` 的其他值缺少元组，`<from_source3>` 不对其返回任何值。  
  
*JOIN 示例 3，具有 3 个源：*  
  
- 让 <from_source1> 的范围为集合，并表示集 {A, B, C}。  
  
- 让 `<from_source1>` 的范围为集合，并表示集 {A, B, C}。  
  
- <from_source2> 作为引用 input_alias1 的文档作用域，并表示集：  
  
    若 `input_alias1 = A,`，表示集 {1, 2}  
  
    若 `input_alias1 = B,`，表示集 {3}  
  
    若 `input_alias1 = C,`，表示集 {4, 5}  
  
- 让 `<from_source3>` 的范围限定为 `input_alias1`，表示的集如下所示：  
  
    若 `input_alias2 = A,`，表示集 {100, 200}  
  
    若 `input_alias2 = C,`，表示集 {300}  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 将产生以下元组：  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
> [!NOTE]
> 这会导致 `<from_source2>` 和 `<from_source3>` 产生叉积，因为两者的范围都为 `<from_source1>`。  这会产生 4 (2x2) 个具有 A 值的元组、0 个具有 B (1x0) 值的元组、和 2 (2x1) 个具有 C 值的元组。  
  
**另请参阅**  
  
 [SELECT 子句](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> WHERE 子句  
 为查询返回的文档指定搜索条件。  
  
 **语法**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **参数**  
  
-   `<filter_condition>`  
  
     指定返回的文档需满足的条件。  
  
-   `<scalar_expression>`  
  
     表示要计算的值的表达式。 请参阅[标量表达式](#bk_scalar_expressions)部分，了解详细信息。  
  
 **备注**  
  
 为了能够返回文档，指定为筛选条件的表达式的求值结果必须为 true。 只有当布尔值为 true 才可以满足条件，任何其他值：未定义、null、false、数字、数组或对象均不满足条件。  
  
##  <a name="bk_orderby_clause"></a> ORDER BY 子句  
 为查询返回的结果指定排序顺序。  
  
 **语法**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **参数**  
  
-   `<sort_specification>`  
  
     指定查询结合集要进行排序的属性或表达式。 排序列可以指定为名称或列别名。  
  
     可以指定多个排序列。 列名必须唯一。 ORDER BY 子句中排序列的序列定义了排序结果集的组织。 即：结果集按第一个属性排序，然后该排序列表按第二个属性排序，依此类推。  
  
     ORDER BY 子句中引用的列名必须确切无误地对应到选择列表中的列或 FROM 子句中指定的表中定义的列。  
  
-   `<sort_expression>`  
  
     指定查询结合集要进行排序的单个属性或表达式。  
  
-   `<scalar_expression>`  
  
     请参阅[标量表达式](#bk_scalar_expressions)部分，了解详细信息。  
  
-   `ASC | DESC`  
  
     指定在指定列中的值应按升序或降序排序。 ASC 采用从最低值到最高值的顺序排序。 DESC 采用从最高值到最低值的顺序排序。 ASC 是默认的排序顺序。 NULL 值被视为可能的最低值。  
  
 **备注**  
  
 虽然查询语法支持多个排序依据属性，但 Azure Cosmos DB 查询运行时支持仅按单个属性排序，以及仅按属性名称排序，即不支持按计算属性排序。 排序也要求索引策略尽可能精确地加入属性和指定类型的范围索引。 有关详细信息，请参阅索引策略文档。  
  
##  <a name="bk_scalar_expressions"></a> 标量表达式  
 标量表达式是符号和运算符的组合，经计算后可获得单个值。 简单表达式可以是常数、属性引用、数组元素引用、别名引用或函数调用。 简单表达式可以使用运算符组合成复杂的表达式。  
  
 有关标量表达式可能包含的值的详细信息，请参阅[常数](#bk_constants)部分。  
  
 **语法**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **参数**  
  
-   `<constant>`  
  
     表示一个常数值。 有关详细信息，请参阅[常数](#bk_constants)部分。  
  
-   `input_alias`  
  
     表示由 `FROM` 子句引入，由 `input_alias` 定义的值。  
    该值不得是“未定义”–输入中的“未定义”值将被跳过。  
  
-   `<scalar_expression>.property_name`  
  
     表示对象的属性值。 如果属性不存在或对非对象的值引用属性，则表达式的求值结果为“未定义”值。  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     使用名称 `property_name` 表示属性的值，使用对象/数组索引 `array_index` 表示数组元素。 如果不存在属性/数组索引，或对非对象/数组的值引用了属性/数组索引，则表达式的求值结果为未定义值。  
  
-   `unary_operator <scalar_expression>`  
  
     表示应用于单个值的运算符。 有关详细信息，请参阅[运算符](#bk_operators)部分。  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     表示应用于两个值的运算符。 有关详细信息，请参阅[运算符](#bk_operators)部分。  
  
-   `<scalar_function_expression>`  
  
     表示由函数调用结果定义的值。  
  
-   `udf_scalar_function`  
  
     用户定义的标量函数的名称。  
  
-   `builtin_scalar_function`  
  
     内置标量函数的名称。  
  
-   `<create_object_expression>`  
  
     表示通过使用指定属性及其值创建一个新对象来获得的值。  
  
-   `<create_array_expression>`  
  
     表示通过使用指定值作为元素创建一个新数组来获得的值。  
  
-   `parameter_name`  
  
     表示指定参数名称的值。 参数名称必须以单个 @ 作为第一个字符。  
  
 **备注**  
  
 调用内置或用户定义的标量函数时，必须定义所有参数。 如果未定义任何参数，则不会调用该函数，且结果也将为未定义。  
  
 创建对象时，将跳过任何已分配未定义值的属性，并且在创建的对象中不包含该属性。  
  
 创建数组时，将跳过任何已分配“未定义”值的元素值，并且在创建的对象中不包含该值。 这将导致下一个定义的元素取代该值时，创建的数组将不含跳过的索引。  
  
##  <a name="bk_operators"></a> 运算符  
 本部分将介绍受支持的运算符。 每个运算符都恰好可以被分配给一个类别。  
  
 有关处理“未定义”值、输入值的类型要求和处理类型不匹配的值的详细信息，请参阅下方的“运算符类别”表。  
  
 运算符类别：  
  
|**类别**|**详细信息**|  
|-|-|  
|算术|运算符要求输入为数字。 输出也应为数字。 如果输入为“未定义”或键入数字外的其他值，则结果为“未定义”。|  
|位|运算符要求输入为 32 位带符号整数。 输出也应为 32 位带符号整数。<br /><br /> 任何非整数值将进行舍入。 正值将向下舍入，负值向上舍入。<br /><br /> 任何超出 32 位整数范围的值都将通过采用最后 32 位的二的补数表示法进行转换。<br /><br /> 如果输入为“未定义”或键入数字外的其他值，则结果为“未定义”。<br /><br /> 注意：上述行为与 JavaScript 位运算符的行为兼容。|  
|逻辑|运算符要求输入为布尔值。 输出也应为布尔值。<br />如果输入“未定义”或键入布尔值外的其他值，则结果为“未定义”。|  
|比较|运算符要求输入的类型应该相同并且不是未定义。 输出应为布尔值。<br /><br /> 如果输入为“未定义”或输入为其他类型，则结果为“未定义”。<br /><br /> 有关值排序的详细信息，请参阅“对要比较的值进行排序”表。|  
|**string**|运算符要求输入为字符串。 输出也应为字符串。<br />如果输入为“未定义”或键入字符串外的其他值，则结果为“未定义”。|  
  
 一元运算符：  
  
|**Name**|运算符|**详细信息**|  
|-|-|-|  
|算术|+<br /><br /> -|返回数字值。<br /><br /> 位求反。 返回求反后的数字值。|  
|位|~|一的补数。 返回数字值的补数。|  
|逻辑|**NOT**|求反。 返回求反后的布尔值。|  
  
 二进制运算符：  
  
|**Name**|运算符|**详细信息**|  
|-|-|-|  
|算术|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|加。<br /><br /> 减。<br /><br /> 乘。<br /><br /> 除。<br /><br /> 取模。|  
|位|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|位或。<br /><br /> 位与。<br /><br /> 位异或。<br /><br /> 左移。<br /><br /> 右移。<br /><br /> 补零右移。|  
|逻辑|**AND**<br /><br /> **或**|逻辑与。 如果两个参数都为 true，则返回 true，否则返回 false。<br /><br /> 逻辑与。 如果两个参数都为 true，则返回 true，否则返回 false。|  
|比较|**=**<br /><br /> !=, <><br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> ??|等于。 如果参数为等于，则返回 "true"，否则返回 "false"。<br /><br /> 不等于。 如果参数为不等于，则返回 "true"，否则返回 "false"。<br /><br /> 大于。 如果第一个参数大于第二个参数，则返回 "true"，否则返回 "false"。<br /><br /> 大于等于。 如果第一个参数大于或等于第二个参数，则返回 "true"，否则返回 "false"。<br /><br /> 小于。 如果第一个参数小于第二个参数，则返回 "true"，否则返回 "false"。<br /><br /> 小于等于。 如果第一个参数小于或等于第二个参数，则返回 "true"，否则返回 "false"。<br /><br /> 联合。 如果第一个参数是“未定义”值，则返回第二个参数。|  
|字符串|**&#124;&#124;**|串联。 返回两个参数的串联。|  
  
 三元运算符：  
  
|三元运算符|?|如果第一个参数求值的结果为 "true"，则返回第二个参数；否则返回第三个参数。|  
|-|-|-|  
  
 对要比较的值进行排序  
  
|**类型**|值顺序|  
|-|-|  
|未定义|不可比较。|  
|Null|单个值：null|  
|**Number**|自然实数。<br /><br /> 负无穷大值小于任何其他数字值。<br /><br /> 正无穷大值大于任何其他数字值。NaN 值不可比较。 与 NaN 进行比较将产生“未定义”值。|  
|字符串|字典顺序。|  
|数组|不排序，但可以相等。|  
|**Object**|不排序，但可以相等。|  
  
 **备注**  
  
 在 Azure Cosmos DB 中，实际上通常在数据库中检索到值时，才知道其类型。 为了支持高效地执行查询，大多数运算符都具有严格的类型要求。 此外运算符本身也不执行隐式转换。  
  
 这意味着 SELECT * FROM ROOT r WHERE r.Age = 21 之类的查询仅返回属性 Age 等于数字 21 的文档。 属性 Age 等于字符串 "21" 或字符串 "0021" 的文档将不匹配，因为表达式 "21" = 21 的求值结果为未定义。 这样可以更好地利用索引，因为查找特定值（即数字 21）比搜索不确定个数的可能匹配项（即数字 21 或字符串 "21"、"021"、"21.0"...）更快。 这种方式与 JavaScript 计算不同类型的值的运算符不同。  
  
 **数组和对象的相等与比较**  
  
 由于没有定义对象或数组值的顺序，使用范围运算符 (>, >=, <, <=) 比较数组或对象的值将产生未定义。 但是，支持使用相等/不等运算符 (=, !=, <>)，并且在结构上比较值。  
  
 如果两个数组具有相同数量的元素，且在匹配位置的元素也相等，则数组相等。 如果比较任何一对元素产生未定义，则数组比较的结果也为未定义。  
  
 如果两个对象定义了相同的属性，且匹配属性的值也相等，则对象相等。 如果比较任何一对属性值产生未定义，则对象比较的结果也为未定义。  
  
##  <a name="bk_constants"></a> 常量  
 常数也称为文本值或标量值，是表示特定数据值的符号。 常数的格式取决于它代表的值的数据类型。  
  
 受支持的标量数据类型：  
  
|**类型**|值顺序|  
|-|-|  
|未定义|单个值：未定义|  
|Null|单个值：null|  
|布尔值|值："false"、"true"。|  
|**数字**|双精度浮点数，IEEE 754 标准。|  
|字符串|零个或多个 Unicode 字符序列。 字符串必须用单引号或双引号括起来。|  
|数组|零个或多个元素序列。 每个元素可以是任何标量数据类型的值，未定义除外。|  
|**Object**|零个或多个名称/值对的无序集。 当名称为一个Unicode字符串，值可以属于任何标量数据类型，“未定义”除外。|  
  
 **语法**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **参数**  
  
1.  `<undefined_constant>; undefined`  
  
     表示 Undefined 类型的未定义值。  
  
2.  `<null_constant>; null`  
  
     表示 Null 类型的 null 值。  
  
3.  `<boolean_constant>`  
  
     表示 Boolean 类型的常数。  
  
4.  `false`  
  
     表示 Boolean 类型的 false 值。  
  
5.  `true`  
  
     表示 Boolean 类型的 true 值。  
  
6.  `<number_constant>`  
  
     表示常数。  
  
7.  `decimal_literal`  
  
     十进制文本是使用十进制表示法或科学记数法表示的数字。  
  
8.  `hexadecimal_literal`  
  
     十六进制文本是使用 "0x" 前缀表示的数字，后跟一个或多个十六进制数字。  
  
9. `<string_constant>`  
  
     表示字符串类型的常数。  
  
10. `string _literal`  
  
     字符串文本是以零个或多个 Unicode 字符序列或转义符序列表示的 Unicode 字符串。 字符串文本括在单引号 (') 或双引号 (") 中。  
  
 允许以下转义序列：  
  
|**转义序列**|**说明**|**Unicode 字符**|  
|-|-|-|  
|\\'|撇号 (')|U+0027|  
|\\"|引号 (")|U+0022|  
|\\\|反斜线号 (\\)|U+005C|  
|\\/|斜线号 (/)|U+002F|  
|\b|退格符|U+0008|  
|\f|换页符|U+000C|  
|\n|换行符|U+000A|  
|\r|回车符|U+000D|  
|\t|Tab|U+0009|  
|\uXXXX|由 4 个十六进制数字定义的 Unicode 字符。|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> 查询性能准则  
 为了能够对大型集合高效地执行查询，应使用可通过由一个或多个索引服务的筛选器。  
  
 以下筛选器将考虑用于索引查找：  
  
-   对文档路径表达式和常数使用相等运算符 ( = )。  
  
-   对文档路径表达式和数字常数使用范围运算符 (<, \<=, >, >=)。  
  
-   文档路径表达式代表任何可从引用的数据集合识别文档中常量路径的表达式。  
  
 文档路径表达式  
  
 文档路径表达式代表属性或数组索引器评估者在数据库集合文档中的一个文档当中的路径。 此路径可用于直接在数据库集合内的文档中识别筛选器中引用的值的位置。  
  
 若要将表达式视为一个文档路径表达式，它应：  
  
1.  直接引用集合根。  
  
2.  引用某个文档路径表达式的属性或常数数组索引器  
  
3.  引用表示某个文档路径表达式的别名。  
  
     **语法约定**  
  
     下表描述的约定用于说明以下 SQL 引用中的语法。  
  
    |约定|用途|  
    |-|-|    
    |大写|不区分大小写的关键字。|  
    |小写|区分大小写的关键字。|  
    |\<非终止符 >|单独定义的非终止符。|  
    |\<非终止符 > ::=|非终止符的语法定义。|  
    |other_terminal|详细说明的终端（令牌）。|  
    |标识符|标识符。 仅允许以下字符：a-z A-Z 0-9 _第一个字符不能为数字。|  
    |“字符串”|带引号的字符串。 允许任何有效的字符串。 请参阅 string_literal 的说明。|  
    |“符号”|属于语法的一部分的文本符号。|  
    |&#124;（垂直条形图）|语法项的替代项。 只可使用其中一个指定的项。|  
    |[ ] /（括号）|括号项可将一个或多个可选项括起来。|  
    |[ ,...n ]|表示上述项可重复 n 次。 匹配项由逗号分隔。|  
    |[ ...n ]|表示上述项可重复 n 次。 匹配项由空格分隔。|  
  
##  <a name="bk_built_in_functions"></a> 内置函数  
 Azure Cosmos DB 提供多个内置 SQL 函数。 内置函数的类别如下所示。  
  
|函数|说明|  
|--------------|-----------------|  
|[数学函数](#bk_mathematical_functions)|每个数学函数均执行一个计算，通常基于作为参数提供的输出值，并返回数值。|  
|[类型检查函数](#bk_type_checking_functions)|类型检查函数使你能够检查 SQL 查询内表达式的类型。|  
|[字符串函数](#bk_string_functions)|该字符串函数对字符串输入值执行操作，并返回字符串、数值或布尔值。|  
|[数组函数](#bk_array_functions)|该数组函数对数组输入值执行操作，并返回数值、布尔值或数组值。|  
|[空间函数](#bk_spatial_functions)|该空间函数对控件对象输入值执行操作，并返回数值或布尔值。|  
  
###  <a name="bk_mathematical_functions"></a> 数学函数  
 以下函数均执行一个计算，通常基于作为参数提供的输出值，并返回数值。  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 返回指定数值表达式的绝对（正）值。  
  
 **语法**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例显示了对三个不同数字使用 ABS 函数的结果。  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 结果集如下。  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 返回角度（弧度），其余弦是指定的数值表达式；也被称为反余弦。  
  
 **语法**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回 -1 的反余弦值。  
  
```  
SELECT ACOS(-1)  
```  
  
 结果集如下。  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 返回角度（弧度），其正弦是指定的数值表达式。 也被称为反正弦。  
  
 **语法**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回  -1 的反正弦值。  
  
```  
SELECT ASIN(-1)  
```  
  
 结果集如下。  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 返回角度（弧度），其正切是指定的数值表达式。 这也被称为反正切。  
  
 **语法**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回指定值的反正切值。  
  
```  
SELECT ATAN(-45.01)  
```  
  
 结果集如下。  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 返回 y/x 的反正切函数的主值（以弧度表示）。  
  
 **语法**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例可计算指定的 x 分量和 y 分量的 ATN2 值。  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 结果集如下。  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 返回大于或等于指定数值表达式的最小整数值。  
  
 **语法**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例显示了 CEILING 函数的正数、负数和零值。  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 结果集如下。  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 返回指定表达式中指定角度的三角余弦（弧度）。  
  
 **语法**  
  
```  
COS(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例可计算指定角的余弦值。  
  
```  
SELECT COS(14.78)  
```  
  
 结果集如下。  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 返回指定数值表达式中指定角度的三角余切。  
  
 **语法**  
  
```  
COT(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例可计算指定角的余切值。  
  
```  
SELECT COT(124.1332)  
```  
  
 结果集如下。  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 返回指定角度（弧度）的相应角度（度）。  
  
 **语法**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回 PI/2 弧度角的度数。  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 结果集如下。  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 返回小于或等于指定数值表达式的最大整数。  
  
 **语法**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例显示了 FLOOR 函数的正数、负数和零值。  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 结果集如下。  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 返回指定数值表达式的指数值。  
  
 **语法**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **备注**  
  
 常数 e  (2.718281…) 是自然对数的底数。  
  
 某个数为指数表示常数 e 自乘的次数为该数。 例如 EXP(1.0) = e^1.0 = 2.71828182845905，EXP(10) = e^10 = 22026.4657948067。  
  
 某个数的自然对数的指数就是该数本身：EXP (LOG (n)) = n。 并且某个数的指数的自然对数也是该数本身：LOG (EXP (n)) = n。  
  
 **示例**  
  
 以下示例声明一个变量并返回到指定变量 (10) 的指数值。  
  
```  
SELECT EXP(10)  
```  
  
 结果集如下。  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 以下示例将返回 20 的自然对数的指数值以及 20 的指数的自然对数。 由于这些函数互为反函数，因此在这两种情况下，用于浮点数学舍入的返回值均为 20。  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 结果集如下。  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 返回指定数值表达式的自然对数。  
  
 **语法**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
-   `base`  
  
     设置对数底数的可选数值参数。  
  
 返回类型  
  
 返回数值表达式。  
  
 **备注**  
  
 默认情况下，LOG() 返回自然对数。 可以使用可选底数参数将对数的底数改为另一个值。  
  
 自然对数是以 e 为底数的对数，其中 e 是一个约等于2.718281828 的无理数常数。  
  
 某个数的指数的自然对数就是该数本身：LOG( EXP( n ) ) = n。 并且某个数的自然对数的指数也是该数字本身：EXP( LOG( n ) ) = n。  
  
 **示例**  
  
 以下示例声明一个变量并返回指定变量 (10) 的对数值。  
  
```  
SELECT LOG(10)  
```  
  
 结果集如下。  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 以下示例可计算某个数的指数的对数值。  
  
```  
SELECT EXP(LOG(10))  
```  
  
 结果集如下。  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 返回指定数值表达式中以 10 为底数的对数。  
  
 **语法**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **备注**  
  
 LOG10 和 POWER 函数互为反函数。 例如，10 ^ LOG10(n) = n。  
  
 **示例**  
  
 以下示例声明一个变量并返回到指定变量 (100) 的 LOG10 值。  
  
```  
SELECT LOG10(100)  
```  
  
 结果集如下。  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 返回 PI 的常数值。  
  
 **语法**  
  
```  
PI ()  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回 PI 值。  
  
```  
SELECT PI()  
```  
  
 结果集如下。  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 返回指定表达式相对指定幂的值。  
  
 **语法**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
-   `y`  
  
     为 `numeric_expression` 自乘得到的幂。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例演示了某个数自乘 3 次的幂（该数的立方）。  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 结果集如下。  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 返回输入的数值表达式（度）的弧度。  
  
 **语法**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将几个角作为输入，然后返回其对应的弧度值。  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 结果集如下。  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 返回一个数值，四舍五入到最接近的整数值。  
  
 **语法**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将以下的正数和负数舍入到最接近的整数。  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 结果集如下。  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 返回指定数值表达式的正数 (+1)、零 (0) 或负数 (-1)。  
  
 **语法**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回数字 -2 到 2 的符号函数值。  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 结果集如下。  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 返回指定表达式中指定角度的三角正弦（弧度）。  
  
 **语法**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例可计算指定角的正弦值。  
  
```  
SELECT SIN(45.175643)  
```  
  
 结果集如下。  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 返回指定数值的平方根。  
  
 **语法**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回数字 1-3 的平方根。  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 结果集如下。  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 返回指定数字值的平方。  
  
 **语法**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回数字 1-3 的平方。  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 结果集如下。  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 返回指定表达式中指定角度（弧度）的正切值。  
  
 **语法**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例可计算 PI()/2 的正切值。  
  
```  
SELECT TAN(PI()/2);  
```  
  
 结果集如下。  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 返回一个数值，截断到最接近的整数值。  
  
 **语法**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **参数**  
  
-   `numeric_expression`  
  
     为数值表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将以下的正数和负数截断为最接近的整数。  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 结果集如下。  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> 类型检查函数  
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
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例使用 IS_ARRAY 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 结果集如下。  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 返回一个布尔值，指示指定表达式的类型是否为布尔表达式。  
  
 **语法**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **参数**  
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例使用 IS_BOOL 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 结果集如下。  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 返回一个布尔，它指示属性是否已经分配了值。  
  
 **语法**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **参数**  
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例检查了指定的 JSON 文档内是否存在属性。 由于存在 "a"，第一个会返回 true，但由于不存在 "b"，第二个会返回 false。  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 结果集如下。  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 返回一个布尔值，指示指定表达式的类型是否为 null。  
  
 **语法**  
  
```  
IS_NULL(<expression>)  
```  
  
 **参数**  
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例使用 IS_NULL 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 结果集如下。  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 返回一个布尔值，指示指定表达式的类型是否为数字。  
  
 **语法**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **参数**  
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例使用 IS_NULL 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 结果集如下。  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 返回一个布尔值，指示指定表达式的类型是否为 JSON 对象。  
  
 **语法**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **参数**  
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例使用 IS_OBJECT 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 结果集如下。  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 返回一个布尔值，指示指定表达式的类型是否为一个（字符串、布尔、数值或 null）。  
  
 **语法**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **参数**  
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例使用 IS_PRIMITIVE 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 结果集如下。  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 返回一个布尔值，指示指定表达式的类型是否为字符串。  
  
 **语法**  
  
```  
IS_STRING(<expression>)  
```  
  
 **参数**  
  
-   `expression`  
  
     为任何有效的表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例使用 IS_STRING 函数检查了 JSON 布尔值、数字、字符串、null、对象、数组和未定义类型的对象。  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 结果集如下。  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> 字符串函数  
 下面的标量函数对字符串输入值执行操作，并返回字符串、数值或布尔值。  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[UPPER](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 返回一个字符串，该字符串是连接两个或多个字符串值的结果。  
  
 **语法**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例将返回指定值的串联字符串。  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 结果集如下。  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串表达式。  
  
 **语法**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例检查了 "abc" 是否包含 "ab" 且包含 "d"。  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 结果集如下。  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。  
  
 **语法**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例将返回以 "b" 和 "bc" 结尾的 "abc"。  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 结果集如下。  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 返回第一个指定的字符串表达式中第一次出现第二个字符串表达式的起始位置，如果未找到字符串，则返回 -1。  
  
 **语法**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例将返回 "abc" 内不同子字符串的索引。  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 结果集如下。  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 返回具有指定字符数的字符串的左侧部分。  
  
 **语法**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
-   `num_expr`  
  
     为任何有效的数值表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例将返回不同长度值的 "abc" 的左侧部分。  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 结果集如下。  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 返回指定字符串表达式的字符数。  
  
 **语法**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例将返回字符串的长度。  
  
```  
SELECT LENGTH("abc")  
```  
  
 结果集如下。  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 返回在将大写字符数据转换为小写后的字符串表达式。  
  
 **语法**  
  
```  
LOWER(<str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例介绍了如何在查询中使用 LOWER。  
  
```  
SELECT LOWER("Abc")  
```  
  
 结果集如下。  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 返回删除前导空格后的字符串表达式。  
  
 **语法**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例介绍了如何在查询中使用 LTRIM。  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 结果集如下。  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 将出现的所有指定字符串值替换为另一个字符串值。  
  
 **语法**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例介绍了如何在查询中使用 REPLACE。  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 结果集如下。  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 将一个字符串值重复指定的次数。  
  
 **语法**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
-   `num_expr`  
  
     为任何有效的数值表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例介绍了如何在查询中使用 REPLICATE。  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 结果集如下。  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 返回字符串值的逆序排序形式。  
  
 **语法**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例介绍了如何在查询中使用 REVERSE。  
  
```  
SELECT REVERSE("Abc")  
```  
  
 结果集如下。  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 返回具有指定字符数的字符串的右侧部分。  
  
 **语法**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
-   `num_expr`  
  
     为任何有效的数值表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例将返回不同长度值的 "abc" 的右侧部分。  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 结果集如下。  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 在删除尾随空格后，返回字符串表达式。  
  
 **语法**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例介绍了如何在查询中使用 RTRIM。  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 结果集如下。  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。  
  
 **语法**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例检查了字符串 "abc" 是否以 "b" 和 "a" 开头。  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 结果集如下。  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> SUBSTRING  
 返回字符串表达式的部分内容，该内容起于指定字符从零开始的位置，继续到指定长度或字符串结尾。  
  
 **语法**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
-   `num_expr`  
  
     为任何有效的数值表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例将返回 "abc" 中从位置 1 开始且长度为 1 个字符的子字符串。  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 结果集如下。  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> UPPER  
 返回在将小写字符数据转换为大写后的字符串表达式。  
  
 **语法**  
  
```  
UPPER(<str_expr>)  
```  
  
 **参数**  
  
-   `str_expr`  
  
     为任何有效的字符串表达式。  
  
 返回类型  
  
 返回字符串表达式。  
  
 **示例**  
  
 以下示例介绍了如何在查询中使用 UPPER。  
  
```  
SELECT UPPER("Abc")  
```  
  
 结果集如下。  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> 数组函数  
 下面的标量函数对数组输入值执行操作，并返回数值、布尔值或数组值  
  
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
  
-   `arr_expr`  
  
     为任何有效的数组表达式。  
  
 返回类型  
  
 返回数组表达式。  
  
 **示例**  
  
 以下示例介绍了如何连接两个数组。  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 结果集如下。  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
返回一个布尔，它指示数组是否包含指定的值。 可以指定是要执行完全还是部分匹配。 

 **语法**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **参数**  
  
-   `arr_expr`  
  
     为任何有效的数组表达式。  
  
-   `expr`  
  
     为任何有效的表达式。  

-   `bool_expr`  
  
     为任何布尔表达式。       
  
 返回类型  
  
 返回一个布尔值。  
  
 **示例**  
  
 以下示例介绍了如何使用 ARRAY_CONTAINS 检查数组内的成员资格。  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 结果集如下。  
  
```  
[{"$1": true, "$2": false}]  
```  

 以下示例介绍了如何使用 ARRAY_CONTAINS 检查数组内是否存在 JSON 字符串的部分匹配字符串。  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 结果集如下。  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 返回指定数组表达式的元素数。  
  
 **语法**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **参数**  
  
-   `arr_expr`  
  
     为任何有效的数组表达式。  
  
 返回类型  
  
 返回数值表达式。  
  
 **示例**  
  
 以下示例介绍了如何使用 ARRAY_LENGTH 获得数组的长度。  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 结果集如下。  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 返回部分数组表达式。
  
 **语法**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **参数**  
  
-   `arr_expr`  
  
     为任何有效的数组表达式。  
  
-   `num_expr`  
  
     为任何有效的数值表达式。  
  
 返回类型  
  
 返回一个布尔值。  
  
 **示例**  
  
 以下示例介绍了如何使用 ARRAY_SLICE 获得部分数组。  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 结果集如下。  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> 空间函数  
 以下标量函数对标量对象输入值执行操作，并返回数值或布尔值。  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 返回两个 GeoJSON 点、多边形或 LineString 表达式之间的距离。  
  
 **语法**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **参数**  
  
-   `spatial_expr`  
  
     为任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
  
 返回类型  
  
 返回包含距离的数值表达式。 默认参考系统以米为单位表示。  
  
 **示例**  
  
 以下示例介绍了如何使用 ST_DISTANCE 内置函数返回在指定位置 30 km 内的所有家族文档。 。  
  
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
  
-   `spatial_expr`  
  
     为任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
 
-   `spatial_expr`  
  
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
  
-   `spatial_expr`  
  
     为任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
 
-   `spatial_expr`  
  
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
  
-   `spatial_expr`  
  
     为任何有效的 GeoJSON 点、多边形或 LineString 表达式。  
  
 返回类型  
  
 返回一个布尔表达式。  
  
 **示例**  
  
 以下示例介绍了如何使用 ST_VALID 检查点是否有效。  
  
 例如，由于此点具有一个不在有效值范围 [-90，90] 内的纬度值，因此查询返回 false。  
  
 对于多边形，GeoJSON 规范要求提供的最后一个坐标対应该与第一个坐标对相同，才能创建一个闭合形状。 多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 结果集如下。  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 如果指定的 GeoJSON 点、多边形或 LineString 表达式有效，则返回包含布尔值的 JSON 值；如果无效，则额外加上作为字符串值的原因。  
  
 **语法**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **参数**  
  
-   `spatial_expr`  
  
     任何有效的 GeoJSON 点或多边形表达式。  
  
 返回类型  
  
 如果指定的 GeoJSON 点或多边形表达式有效，则返回包含布尔值的 JSON 值；如果无效，则额外添加原因作为字符串值。  
  
 **示例**  
  
 以下示例介绍了如何使用 ST_ISVALIDDETAILED 检查有效性（附带详细信息）。  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 结果集如下。  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>后续步骤  
 [Azure Cosmos DB 的 SQL 语法和 SQL 查询 ](sql-api-sql-query.md)   
 [Azure Cosmos DB 文档](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
