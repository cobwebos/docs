---
title: 用于在 Azure Active Directory 中编写属性映射的表达式的参考
description: 了解如何在 Azure Active Directory 中自动预配 SaaS 应用对象期间，使用表达式映射将属性值转换为可接受的格式。 包括函数的引用列表。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: kenwith
ms.openlocfilehash: 14e3b23b4246f26e1ac59e0b12b043341546d0a0
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018237"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>用于在 Azure AD 中编写属性映射的表达式的参考

将预配配置到 SaaS 应用程序时，表达式映射是可指定的属性映射类型之一。 为此，必须编写一个类似于脚本的表达式，允许将用户的数据转换为 SaaS 应用程序更可接受的格式。

## <a name="syntax-overview"></a>语法概述

属性映射的表达式语法让人联想到 Visual Basic for Applications (VBA) 函数。

* 整个表达式必须按函数定义，其中包含名称后跟括号中的参数： *FunctionName (`<<argument 1>>` ， `<<argument N>>`) *
* 函数之间可以相互嵌套。 例如：  *FunctionOne (FunctionTwo (`<<argument1>>`) # B3 *
* 可以将三种不同类型的参数传递给函数：
  
  1. 属性，必须括在方括号中。 例如：[attributeName]
  2. 字符串常量必须括在双引号内。 例如："美国"
  3. 其他函数。 例如： FunctionOne (`<<argument1>>` ，FunctionTwo (`<<argument2>>`) # A3
* 对于字符串常量，如果字符串中需要反斜杠 ( \ ) 或引号 ( " )，则必须使用反斜杠 ( \ ) 符号进行转义。 例如： "公司名称： \\ " Contoso \\ ""
* 语法区分大小写，在函数中将它们键入为字符串时必须考虑该语法，与从源直接复制它们。 

## <a name="list-of-functions"></a>函数列表

[Append](#append) &nbsp; &nbsp; 追加 &nbsp; &nbsp;[BitAnd](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp;[CBool](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp;[Coalesce](#coalesce) &nbsp; &nbsp; 合并 &nbsp; &nbsp;[ConvertToBase64](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp;[ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp;[Count](#count) &nbsp; &nbsp; 计数 &nbsp; &nbsp;[CStr](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp;[DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp;[Guid](#guid) &nbsp; &nbsp; Guid &nbsp; &nbsp;[IIF](#iif) &nbsp; &nbsp; IIF &nbsp; &nbsp;[InStr](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp;[IsNull](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp;[IsNullOrEmpty](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp;[IsPresent](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp;[IsString](#isstring) &nbsp; &nbsp; IsString &nbsp; &nbsp;[Item](#item) &nbsp; &nbsp; 项 &nbsp; &nbsp;[Join](#join) &nbsp; &nbsp; 联接 &nbsp; &nbsp;[Left](#left) &nbsp; &nbsp; 左 &nbsp; &nbsp;[Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [Replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word) StripSpaces ToLower ToUpper Word

---
### <a name="append"></a>附加

**函数：** 追加 (源，后缀) 

**说明：** 获取源字符串值，然后将后缀追加到该字符串的末尾。

**Parameters**

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称。 |
| **suffix** |必须 |String |要附加到源值末尾的字符串。 |

---
### <a name="bitand"></a>BitAnd
**函数：** BitAnd (value1，value2) 

**说明：** 此函数将两个参数都转换为二进制表示形式，并将位设置为：

- 0 - 如果 value1 和 value2 中的一个或两个相应位为 0
- 1 - 如果两个相应位均为 1。

换而言之，除了当两个参数的相应位均为 1 时之外，所有情况下均返回 0。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **value1** |必须 |num |应与 value2 一起个的数值|
| **value2** |必须 |num |应为 value1 个的数字值|

**实例**
`BitAnd(&HF, &HF7)`

11110111和 00000111 = 00000111 `BitAnd` ，因此返回7，二进制值为00000111。

---
### <a name="cbool"></a>CBool
**才能** 
`CBool(Expression)`

**说明：**  
 `CBool`返回基于计算的表达式的布尔值。 如果表达式的计算结果为非零值，则 `CBool` 返回 *True*，否则返回 *False*。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必须 | 表达式 | 任何有效的表达式 |

示例：  
`CBool([attribute1] = [attribute2])`                                                                    
如果两个属性具有相同的值，则返回 True。

---
### <a name="coalesce"></a>Coalesce
**函数：** 合并 (source1，source2，...，defaultValue) 

**说明：** 返回不为 NULL 的第一个源值。 如果所有参数均为 NULL，并且 defaultValue 存在，则将返回 defaultValue。 如果所有参数均为 NULL，并且 defaultValue 不存在，则合并将返回 NULL。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source1 .。。sourceN** | 必须 | String |必需，次数可变。 通常是来自源对象的属性的名称。 |
| **defaultValue** | 可选 | 字符串 | 所有源值为 NULL 时要使用的默认值。 可以是空字符串 ("")。

---
### <a name="converttobase64"></a>ConvertToBase64
**函数：** ConvertToBase64 (源) 

**说明：** ConvertToBase64 函数将字符串转换为 Unicode base64 字符串。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要转换为 base 64 的字符串|

**实例**
`ConvertToBase64("Hello world!")`

返回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**函数：** ConvertToUTF8Hex (源) 

**说明：** ConvertToUTF8Hex 函数将字符串转换为 UTF8 十六进制编码值。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要转换为 UTF8 Hex 的字符串|

**实例**
`ConvertToUTF8Hex("Hello world!")`

返回 48656C6C6F20776F726C6421

---
### <a name="count"></a>计数
**函数：** 计数 (特性) 

**说明：** Count 函数返回多值属性中的元素数

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **attribute** |必须 |attribute |将计算元素的多值属性|

---
### <a name="cstr"></a>CStr
**函数：** CStr (值) 

**说明：** CStr 函数将值转换为字符串数据类型。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| value  |必须 | numeric、reference 或 boolean | 可以是数字值、引用属性或布尔值。 |

**实例**
`CStr([dn])`

返回 "cn = Joe，dc = contoso，dc = com"

---
### <a name="datefromnum"></a>DateFromNum
**函数：** DateFromNum (值) 

**说明：** DateFromNum 函数将 AD 的日期格式的值转换为日期时间类型。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| value  |必须 | 日期 | 要转换为 DateTime 类型的广告日期 |

**实例**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

返回表示2012年1月1日的日期时间，在11： 00 (。

---
### <a name="formatdatetime"></a>FormatDateTime
**函数：** FormatDateTime (source、inputFormat、outputFormat) 

**说明：** 从一种格式获取日期字符串，并将其转换为其他格式。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称。 |
| **inputFormat** |必须 |String |源值的预期格式。 有关支持的格式，请参阅 [/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |
| **outputFormat** |必须 |String |输出日期的格式。 |

---
### <a name="guid"></a>Guid
**函数：** Guid ( # A1

**说明：** 函数 Guid 生成新的随机 GUID

---
### <a name="iif"></a>IIF
**函数：** IIF (condition、valueIfTrue、valueIfFalse) 

**说明：** IIF 函数基于指定的条件返回一组可能值中的一个值。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **状态** |必须 |变量或表达式 |计算结果为 true 或 false 的任何值或表达式。 |
| **valueIfTrue** |必须 |变量或字符串 | 如果条件计算结果为 true，则为返回值。 |
| **valueIfFalse** |必须 |变量或字符串 |如果条件计算结果为 false，则为返回值。|

**实例**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**函数：** InStr (value1，value2，start，compareType) 

**说明：** InStr 函数查找字符串中第一次出现的子字符串

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **value1** |必须 |String |要搜索的字符串 |
| **value2** |必须 |String |要查找的字符串 |
| **start** |可选 |整数 |用于查找子字符串的起始位置|
| **compareType** |可选 |枚举 |可以是 vbTextCompare 或 vbBinaryCompare |

**实例**
`InStr("The quick brown fox","quick")`

计算结果为5

`InStr("repEated","e",3,vbBinaryCompare)`

计算结果为 7

---
### <a name="isnull"></a>IsNull
**函数：** IsNull (表达式) 

**说明：** 如果表达式的计算结果为 Null，则 IsNull 函数返回 true。 对于属性，Null 表示缺少属性。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必须 |表达式 |要计算的表达式 |

**实例**
`IsNull([displayName])`

如果该属性不存在，则返回 True。

---
### <a name="isnullorempty"></a>IsNullorEmpty
**函数：** IsNullOrEmpty (表达式) 

**说明：** 如果表达式为 null 或空字符串，则 IsNullOrEmpty 函数返回 true。 对于属性，如果属性不存在，或存在但为空字符串，此语法计算结果则为 True。
此函数的逆函数命名为 IsPresent。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必须 |表达式 |要计算的表达式 |

**实例**
`IsNullOrEmpty([displayName])`

如果该属性不存在或为空字符串，则返回 True。

---
### <a name="ispresent"></a>IsPresent
**函数：** IsPresent (表达式) 

**说明：** 如果表达式的计算结果为非 Null 且不为空的字符串，则 IsPresent 函数返回 true。 此函数的逆函数被命名为 IsNullOrEmpty。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必须 |表达式 |要计算的表达式 |

**实例**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**函数：** IsString (表达式) 

**说明：** 如果表达式可以计算为字符串类型，则 IsString 函数的计算结果为 True。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必须 |表达式 |要计算的表达式 |

---
### <a name="item"></a>项
**函数：** Item (属性，index) 

**说明：** Item 函数返回多值字符串/属性中的一个项。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **attribute** |必须 |Attribute |要搜索的多值属性 |
| **index** |必需 |整数 | 多值字符串中的项的索引|

**实例**
`Item([proxyAddresses], 1)`

---
### <a name="join"></a>联接
**函数：** 联接 (separator，source1，source2，... ) 

**说明：** 联接 ( # A1 类似于追加 ( # A3，只不过它可以将多个 **源** 字符串值组合成单个字符串，每个值将由一个 **分隔符** 字符串分隔。

如果其中一个源值是多值属性，那么该属性中的每个值都将联接在一起，由分隔符值分隔。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| separator  |必须 |String |用于在将源值连接为一个字符串时分隔源值的字符串。 如果不需要分隔符，则可以是 ""。 |
| **source1 .。。sourceN** |必选，次数可变 |字符串 |要联接在一起的字符串值。 |

---
### <a name="left"></a>Left
**函数：** Left (String，NumChars) 

**说明：** Left 函数从字符串左侧起返回指定数目的字符。 如果 numChar = 0，则返回空字符串。
如果 numChar < 0，则返回输入字符串。
如果字符串为 null，则返回空字符串。
如果字符串包含的字符数比 numChars 中指定的数量少，则返回与该字符串相同的字符串（即，包含参数 1 中的所有字符）。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **字符串** |必须 |Attribute | 要从其返回字符的字符串 |
| **NumChars** |必需 |整数 | 标识从字符串的开头开始)  (的字符数的数字|

**实例**
`Left("John Doe", 3)`

返回 “Joh”。

---
### <a name="mid"></a>Mid
**函数：** Mid (源，开始，长度) 

**说明：** 返回源值的子字符串。 子字符串是一个只包含源字符串中某些字符的字符串。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是属性的名称。 |
| **start** |必须 |整型 |**source** 字符串中的索引，子字符串应从这里开始。 字符串中第一个字符的索引为 1，第二个字符的索引为 2，依此类推。 |
| **length** |必须 |整型 |子字符串的长度。 如果长度超出 **source** 字符串，则函数将返回从 **start** 索引到 **source** 字符串末尾的子字符串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**函数：** NormalizeDiacritics (源) 

**说明：** 需要一个字符串参数。 返回字符串，但将任何标注字符替换为等效的非标注字符。 通常用于将包含标注字符（重音符号）的名字和姓氏转换为可用于各种用户标识符（例如用户主体名称、SAM 帐户名称和电子邮件地址）的合法值。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String | 通常是名字或姓氏属性。 |

---
### <a name="not"></a>Not
**函数：** 不 (源) 

**说明：** 翻转 **源**的布尔值。 如果 **source** 值为 True，则返回 False。 否则，返回 True。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |布尔型字符串 |预期的 **source** 值为“True”或“False”。 |

---
### <a name="numfromdate"></a>NumFromDate
**函数：** NumFromDate (值) 

**说明：** NumFromDate 函数将 DateTime 值转换为设置属性（如 [accountExpires](/windows/win32/adschema/a-accountexpires)）所需的 Active Directory 格式。 使用此函数将从 Workday 和 SuccessFactors 之类的云 HR 应用接收的日期时间值转换为其等效的 AD 表示形式。 

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| value  |必须 | String | 支持的格式的日期时间字符串。 有关支持的格式，请参阅 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx。 |

**示例：**
* Workday 示例假设你想要将*ContractEndDate*中的属性（格式为*2020-12-31-08:00*到 accountExpires 字段）映射到 AD 中的*accountExpires*字段，则可以使用此函数并更改时区偏移量以匹配你的区域设置。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors 示例假设你想要将属性 *结束* 时间从 SuccessFactors （格式为 *M/d/yyyy hh： mm： ss tt* ）映射到 AD 中的 *accountExpires* 字段，以下是你如何使用此函数并更改时区偏移量以匹配你的区域设置。
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**函数：** RemoveDuplicates (特性) 

**说明：** RemoveDuplicates 函数使用多值字符串，并确保每个值都是唯一的。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **attribute** |必须 |多值属性 |将删除重复项的多值属性|

**示例：** 
 `RemoveDuplicates([proxyAddresses])`返回净化的 proxyAddress 属性，其中所有重复值均已删除。

---
### <a name="replace"></a>Replace
**函数：** 替换 (source，oldValue，regexPattern，regexGroupName，replacementValue，replacementAttributeName，template) 

**说明：** 替换字符串中的值。 其工作方式取决于提供的参数：

* 当提供了 **oldValue** 和 **replacementValue** 时：
  
  * 将**源**中出现的所有**OldValue**替换为**replacementValue**
* 当提供了 **oldValue** 和 **template** 时：
  
  * 将 **template** 中出现的所有 **oldValue** 替换为 **source** 值
* 如果已提供 **regexPattern** 和 **replacementValue**：

  * 函数将 **regexPattern** 应用到 **source** 字符串，你可以使用 regex 组名称来构造 **replacementValue** 的字符串。
* 当提供 **regexPattern**、**regexGroupName**、**replacementValue** 时：
  
  * 函数将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为 **replacementValue**
* 如果已提供 **regexPattern**、**regexGroupName** 和 **replacementAttributeName**：
  
  * 如果 **source** 没有值，则返回 **source**
  * 如果 **source** 具有值，则函数会将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为与 **replacementAttributeName** 关联的值

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自 **源** 对象的属性的名称。 |
| **oldValue** |可选 |字符串 |要在 **source** 或 **template** 中替换的值。 |
| **regexPattern** |可选 |字符串 |**source** 中要替换的值的正则表达式模式。 或者，当使用 **replacementPropertyName** 时，从 **replacementPropertyName** 中提取值的模式。 |
| **regexGroupName** |可选 |字符串 |**regexPattern** 中的组名称。 仅当使用 **replacementPropertyName** 时，才会从 **replacementPropertyName** 中以 **replacementValue** 的形式提取此组的值。 |
| **replacementValue** |可选 |字符串 |用于替换旧值的新值。 |
| **replacementAttributeName** |可选 |字符串 |用于替换值的属性的名称 |
| **模版** |可选 |字符串 |提供 **模板** 值后，我们将在模板中查找 **oldValue** ，并将其替换为 **source** 值。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**函数：** SelectUniqueValue (uniqueValueRule1，uniqueValueRule2，uniqueValueRule3，... ) 

**说明：** 至少需要两个参数，这是使用表达式定义的唯一值生成规则。 此函数会评估每个规则，然后在目标应用/目录中检查生成的值的唯一性。 将返回找到的第一个唯一值。 如果所有值都已存在于目标中，则会托管该条目并在审核日志中记录原因。 可以提供的参数数目没有上限。


 - 这是一个顶级函数，不能嵌套。
 - 此函数不能应用到具有匹配优先级的属性。   
 - 此函数仅供用于创建条目。 将其与属性一起使用时，请将“应用映射”**** 属性设置为“仅在创建对象期间”。****
 - 目前只有“Workday 到 Active Directory 的用户预配”支持此函数。 此函数不可用于其他预配应用程序。 


**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |需要至少 2 个，没有上限 |字符串 | 要评估的唯一值生成规则的列表。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**函数：** SingleAppRoleAssignment ( [appRoleAssignments] ) 

**说明：** 从为给定应用程序分配给用户的所有 appRoleAssignments 的列表中返回单个 appRoleAssignment。 需要此函数才能将 appRoleAssignments 对象转换为单个角色名称字符串。 请注意，最佳做法是确保每次只向一个用户分配一个 appRoleAssignment，如果分配了多个角色，则返回的角色字符串可能是不可预测的。 

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必须 |String |**[appRoleAssignments]** 对象。 |

---
### <a name="split"></a>拆分
**函数：** 拆分 (源，分隔符) 

**说明：** 使用指定的分隔符将字符串拆分为多值数组。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要更新的 **source** 值。 |
| **后面** |必须 |String |指定将用来拆分字符串的字符（示例：“,”） |

---
### <a name="stripspaces"></a>StripSpaces
**函数：** StripSpaces (源) 

**说明：** 删除源字符串中 ( "" ) 字符的所有空格。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要更新的 **source** 值。 |

---
### <a name="switch"></a>开关
**函数：** Switch (source，defaultValue，key1，value1，key2，value2，... ) 

**说明：** 当**source**值与某个**键**匹配时，将返回**该项**的**值**。 当 **source** 值未与任何 key 匹配时，则返回 **defaultValue**。  **Key** 和 **value** 参数必须始终成对出现。 该函数始终需要偶数个参数。 函数不应用于管理器等引用属性。 

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |要更新的 **source** 值。 |
| **defaultValue** |可选 |字符串 |当 source 不匹配任何 key 时使用的默认值。 可以是空字符串 ("")。 |
| key  |必须 |String |用来比较 **source** 值的 **key**。 |
| value  |必须 |String |与该 key 匹配的 **source** 的替换值。 |

---
### <a name="tolower"></a>ToLower
**函数：** ToLower (源，区域性) 

**说明：** 获取 *源* 字符串值，并使用指定的区域性规则将其转换为小写。 如果没有指定任何区域性** 信息，则使用固定区域性。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称 |
| **culture** |可选 |字符串 |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2**，其中 languagecode2** 是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码**。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---
### <a name="toupper"></a>ToUpper
**函数：** ToUpper (源，区域性) 

**说明：** 获取 *源* 字符串值，并使用指定的区域性规则将其转换为大写。 如果没有指定任何区域性** 信息，则使用固定区域性。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **source** |必须 |String |通常是来自源对象的属性的名称。 |
| **culture** |可选 |字符串 |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2**，其中 languagecode2** 是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码**。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---
### <a name="word"></a>Word
**函数：** Word (String，WordNumber，分隔符) 

**说明：** Word 函数根据描述要使用的分隔符和要返回的单词编号的参数，返回字符串中包含的单词。 字符串中的字符由分隔符中其中一个字符分隔的每个字符串被标识为单词：

如果数字 < 1，则返回空字符串。
如果字符串为 null，则返回空字符串。
如果字符串包含的单词少于应返回数字或字符串不包含由分隔符标识的任何单词，则返回空字符串。

**Parameters** 

| 名称 | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **字符串** |必须 |多值属性 |要从中返回单词的字符串。|
| **WordNumber** |必需 | 整数 | 标识应返回的单词编号的数字|
| **限定符** |必须 |String| 一个字符串，表示应用于标识单词的分隔符 (s) |

**实例**
`Word("The quick brown fox",3," ")`

返回 "棕色"。

`Word("This,string!has&many separators",3,",!&#")`

返回 "has"。

---

## <a name="examples"></a>示例
### <a name="strip-known-domain-name"></a>删除已知域名
需要从用户的电子邮件中删除已知域名，以便获取用户名。 例如，如果域为“contoso.com”，则可以使用以下表达式：

**表达式** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**示例输入/输出：** 

* **输入** (mail)：“john.doe@contoso.com”
* **输出**：“john.doe”

### <a name="append-constant-suffix-to-user-name"></a>将常量后缀附加到用户名
如果使用 Salesforce 沙箱，则可能需要在进行同步之前向所有用户名附加额外后缀。

**表达式** 
`Append([userPrincipalName], ".test")`

**示例输入/输出：** 

* **输入**：(userPrincipalName)：“John.Doe@contoso.com”
* **输出**： " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>通过连接名字和姓氏部分来生成用户别名
需要使用用户名字的前 3 个字母和用户姓氏的前 5 个字母来生成用户别名。

**表达式** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**示例输入/输出：** 

* **输入** (givenName)：“John”
* **输入** (surname)：“Doe”
* **输出**：“JohDoe”

### <a name="remove-diacritics-from-a-string"></a>从字符串中删除音调符号
需要将包含重音符号的字符替换为不包含重音符号的等效字符。

**表达式：** NormalizeDiacritics ( [givenName] ) 

**示例输入/输出：** 

* **输入** (givenName)：“Zoë”
* **输出**： "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>将字符串拆分为多值数组
你需要获取一个以逗号分隔的字符串列表，将它们拆分为一个数组，可以将该数组插入到多值属性中，例如 Salesforce 的 PermissionSets 属性。 在此示例中，Azure AD 中的 extensionAttribute5 中填充了一个权限集列表。

**表达式：** Split ( [extensionAttribute5]，"，" ) 

**示例输入/输出：** 

* **输入** (extensionAttribute5) ： "PermissionSetOne，PermisionSetTwo"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>输出日期是一种特定格式的字符串
需要以某种格式将日期发送到 SaaS 应用程序。 例如，需要为 ServiceNow 设置日期格式。

**表达式** 

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**示例输入/输出：**

* **输入** (extensionAttribute1)：“20150123105347.1Z”
* **输出**：“2015-01-23”

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根据预定义的选项集替换值

需要根据存储在 Azure AD 中的状态代码来定义用户的时区。 如果状态代码与任何预定义选项都不匹配，则使用默认值“澳大利亚/悉尼”。

**表达式** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**示例输入/输出：**

* **输入** (state)：“QLD”
* **输出**：“澳大利亚/布里斯班”

### <a name="replace-characters-using-a-regular-expression"></a>使用正则表达式替换字符
你需要查找与正则表达式匹配的值并将其删除。

**表达式** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**示例输入/输出：**

* **INPUT** (mailNickname: "john_doe72"
* **输出**： "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>将生成的 userPrincipalName (UPN) 值转换为小写
在下面的示例中，通过串联 PreferredFirstName 和 PreferredLastName 源字段生成 UPN 值，ToLower 函数对生成的字符串进行运算，将所有字符都转换为小写形式。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**： " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>为 userPrincipalName (UPN) 属性生成唯一值
你需要根据用户的名字、中间名和姓氏为 UPN 属性生成值，并在将该值分配给 UPN 属性之前在目标 AD 目录中检查其唯一性。

**表达式** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**： " John.Smith@contoso.com " 如果 UPN 值 John.Smith@contoso.com 在目录中不存在
* **输出**： " J.Smith@contoso.com " John.Smith@contoso.com ，如果目录中已存在 UPN 值
* **输出**： " Jo.Smith@contoso.com " 如果此目录中已存在上述两个 UPN 值

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>流邮件值（如果不为 NULL），否则为流 userPrincipalName
如果邮件属性存在，则需要对其进行流式传输。 如果不是，则您希望改为流式传输 userPrincipalName 的值。

**表达式** 
`Coalesce([mail],[userPrincipalName])`

**示例输入/输出：** 

* **输入** (mail) ： NULL
* **输入** (userPrincipalName) ： " John.Doe@contoso.com "
* **输出**： " John.Doe@contoso.com "

## <a name="related-articles"></a>相关文章
* [在 SaaS 应用中自动预配和取消预配用户](../app-provisioning/user-provisioning.md)
* [为用户预配自定义属性映射](../app-provisioning/customize-application-attributes.md)
* [用于用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [帐户预配通知](../app-provisioning/user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)