---
title: "Azure 服务总线 SQLFilter 语法参考 | Microsoft Docs"
description: "有关 SQLFilter 语法的详细信息。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 3aaec8f9b6a3bbcf814f771405c3b589de6f7ae0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---

# <a name="sqlfilter-syntax"></a>SQLFilter 语法

SqlFilter 是 [SqlFilter 类](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)的实例，代表基于 SQL 语言的筛选器表达式，该表达式针对 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 进行计算。 SqlFilter 支持 SQL-92 标准的子集。  
  
 本主题列出了有关 SqlFilter 语法的详细信息。  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>参数  
  
-   `<scope>` 是一个可选字符串，指示 `<property_name>` 的范围。 有效值为 `sys` 或 `user`。 `sys` 值指示系统范围，其中 `<property_name>` 是 [BrokeredMessage 类](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)的公共属性名称。 `user` 指示用户范围，其中 `<property_name>` 是 [BrokeredMessage 类](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)字典的项。 `user` 范围是默认范围（如果 `<scope>` 未指定）。  
  
## <a name="remarks"></a>备注

尝试访问不存在的系统属性属于错误，而尝试访问不存在的用户属性不属于错误。 不存在的用户属性在内部是作为未知值计算的。 在运算符求值过程中，未知值的处理方式很特殊。  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>参数  

 `<regular_identifier>` 是一个字符串，由以下正则表达式表示：  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
此语法是指任何以字母开头且后跟一个或多个下划线/字母/数字的字符串。  
  
`[:IsLetter:]` 是指其类别为 Unicode 字母的任何 Unicode 字符。 `System.Char.IsLetter(c)` 返回 `true`（如果 `c` 为 Unicode 字母）。  
  
`[:IsDigit:]` 是指其类别为十进制数字的任何 Unicode 字符。 `System.Char.IsDigit(c)` 返回 `true`（如果 `c` 为 Unicode 数字）。  
  
`<regular_identifier>` 不能为保留关键字。  
  
`<delimited_identifier>` 是用左/右方括号 ([]) 括起来的任何字符串。 右方括号采用两个右方括号的形式。 下面是 `<delimited_identifier>` 的示例：  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` 是用双引号括起来的任何字符串。 在标识符中，双引号采用两个双引号的形式。 建议不要使用带引号的标识符，因为很容易与字符串常量混淆。 可能情况下使用分隔的标识符。 下面是 `<quoted_identifier>` 的示例：  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>备注
  
`<pattern>` 必须是以字符串形式求值的表达式， 用作 LIKE 运算符的模式，      并可能包含下列通配符：  
  
-   `%`：包含零个或多个字符的任意字符串。  
  
-   `_`：任何单个字符。  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>备注  

`<escape_char>` 必须是以长度为 1 的字符串形式求值的表达式， 作为转义符用于 LIKE 运算符。  
  
 例如，`property LIKE 'ABC\%' ESCAPE '\'` 与 `ABC%` 匹配，而不是与开头为 `ABC` 的字符串配置。  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>参数  
  
-   `<integer_constant>` 是一个数字字符串，不使用引号，且不包含小数点。 这些值以 `System.Int64` 形式存储在内部，并具有相同的范围。  
  
     下面是长常量的示例：  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` 是一个数字字符串，不使用引号，但包含小数点。 这些值以 `System.Double` 形式存储在内部，并具有相同的范围/精度。  
  
     在未来版本中，此数字可能以其他数据类型存储，目的是支持确切的数字语义，因此不应依赖于 `<decimal_constant>` 的基础数据类型为 `System.Double` 这一事实。  
  
     下面是十进制常量的示例：  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` 是以科学记数法表示的数字。 这些值以 `System.Double` 形式存储在内部，并具有相同的范围/精度。 下面是近似数常量的示例：  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>备注  

布尔常量以关键字 **TRUE** 或 **FALSE** 表示。 这些值以 `System.Boolean` 形式存储。  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>备注  

字符串常量使用单引号，包括任何有效的 Unicode 字符。 嵌入字符串常量中的单引号采用两个单引号的形式。  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>备注
  
`newid()` 函数返回 `System.Guid.NewGuid()` 方法生成的 **System.Guid**。  
  
`property(name)` 函数返回 `name` 所引用的属性的值。 `name` 值可以是任何返回字符串值的有效表达式。  
  
## <a name="considerations"></a>注意事项
  
请考虑以下 [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) 语义：  
  
-   属性名称不区分大小写。  
  
-   运算符尽可能遵循 C# 隐式转换语义。  
  
-   系统属性是在 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 实例中公开的公共属性。  
  
    请考虑以下 `IS [NOT] NULL` 语义：  
  
    -   `property IS NULL` 作为 `true` 求值（如果属性不存在，或者属性的值为 `null`）。  
  
### <a name="property-evaluation-semantics"></a>属性求值语义  
  
-   尝试对不存在的系统属性求值会引发 [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) 异常。  
  
-   不存在的属性在进行内部求值时会被视为**未知**。  
  
 算术运算符中的未知求值：  
  
-   对于二元运算符，如果操作数的左侧和/或右侧的求值结果为**未知**，则结果为**未知**。  
  
-   对于一元运算符，如果操作数的求值结果为**未知**，则结果为**未知**。  
  
 二进制比较运算符中的未知求值：  
  
-   如果操作数的左侧和/或右侧的求值结果为**未知**，则结果为**未知**。  
  
 `[NOT] LIKE` 中的未知求值：  
  
-   如果任何操作数的求值结果为“未知”，则结果为“未知”。  
  
 `[NOT] IN` 中的未知求值：  
  
-   如果左侧操作数的求值结果为“未知”，则结果为“未知”。  
  
 **AND** 运算符中的未知求值：  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 **OR** 运算符中的未知求值：  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>运算符绑定语义
  
-   在进行数据类型提升和隐式转换时，比较运算符（例如 `>`、`>=`、`<`、`<=`、`!=`、`=`）与 C# 运算符绑定遵循相同的语义。  
  
-   在进行数据类型提升和隐式转换时，算术运算符（例如 `+`、`-`、`*`、`/`、`%`）与 C# 运算符绑定遵循相同的语义。

## <a name="next-steps"></a>后续步骤

- [SQLFilter 类](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLRuleAction 类](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
