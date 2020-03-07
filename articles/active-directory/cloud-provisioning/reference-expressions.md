---
title: Azure AD Connect 云预配表达式和函数参考
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51c14fd7f427c29c47521a7355309e62ab2254ca
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298609"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>在 Azure Active Directory 中编写属性映射的表达式
配置云预配时，可以指定的属性映射类型之一是表达式映射。 

通过表达式映射，可以使用类似于脚本的表达式来自定义属性。  这样，便可以将本地数据转换为新的或不同的值。  例如，你可能需要将两个属性合并成单个属性，因为某个云应用程序需使用此单个属性。

以下文档介绍了用于转换数据的类似于脚本的表达式。  这仅仅是转换过程的一部分。  接下来，需要使用此表达式并将其放入对租户发出的 Web 请求中。  有关详细信息，请参阅[转换](how-to-transformation.md)

## <a name="syntax-overview"></a>语法概述
属性映射的表达式语法让人联想到 Visual Basic for Applications (VBA) 函数。

* 必须定义整个表达式的函数，函数中的名称后跟带括号的参数： <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 函数之间可以相互嵌套。 例如： <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 可以将三种不同类型的参数传递给函数：
  
  1. 属性，必须括在方括号中。 例如：[attributeName]
  2. 字符串常量必须括在双引号内。 例如："United States"
  3. 其他函数。 例如：FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 对于字符串常量，如果字符串中需要反斜杠 ( \ ) 或引号 ( " )，则必须使用反斜杠 ( \ ) 符号进行转义。 例如：“Company name:\\"Contoso\\"”

## <a name="list-of-functions"></a>函数列表
| 函数列表 | 说明 |
|-----|----|
|[Append](#append)|获取源字符串值，并将后缀附加到其末尾。|
|[BitAnd](#bitand)|BitAnd 函数设置值的指定位。|
|[CBool](#cbool)|CBool 函数基于计算的表达式返回布尔值|
|[ConvertFromBase64](#convertfrombase64)|ConvertFromBase64 函数将指定的 base64 编码值转换为规则的字符串。|
|[ConvertToBase64](#converttobase64)|ConvertToBase64 函数将字符串转换为 Unicode base64 字符串。 |
|[ConvertToUTF8Hex](#converttoutf8hex)|ConvertToUTF8Hex 函数将字符串转换为 UTF8 Hex 编码的值。|
|[Count](#count)|Count 函数返回多值属性中的元素数量|
|[Cstr](#cstr)|CStr 函数转换为字符串数据类型。|
|[DateFromNum](#datefromnum)|DateFromNum 函数将 AD 的日期格式的值转换为 DateTime 类型。|
|[DNComponent](#dncomponent)|DNComponent 函数返回从左边起的指定 DN 组件的值。|
|[错误](#error)|Error 函数用于返回自定义错误。|
|[FormatDateTime](#formatdatetime) |从一种格式获取日期字符串并将其转换为不同的格式。| 
|[GUID](#guid)|函数 GUID 生成新的随机 GUID。|           
|[IIF](#iif)|IIF 函数基于指定的条件返回一组可能值中的其中一个值。|
|[InStr](#instr)|InStr 函数查找字符串中第一次出现的子字符串。|
|[IsNull](#isnull)|如果表达式的计算结果为 Null，则 IsNull 函数返回 true。|
|[IsNullOrEmpty](#isnullorempty)|如果表达式为 null 或空字符串，则 IsNullOrEmpty 函数返回 true。|         
|[IsPresent](#ispresent)|如果表达式的计算结果为字符串，该字符串不是 Null 且不为空，则 IsPresent 函数返回 true。|    
|[IsString](#isstring)|如果表达式可以计算为字符串类型，则 IsString 函数计算结果为 True。|
|[项](#item)|Item 函数返回多值字符串/属性中的一个项。|
|[Join](#join) |Join() 类似于 Append()，只不过它可以将多个 **source** 字符串值组合到单个字符串中，且每个值由 **separator** 字符串分隔。| 
|[Left](#left)|Left 函数从字符串左侧起返回指定的字符数。|
|[Mid](#mid) |返回源值的子字符串。 子字符串是一个只包含源字符串中某些字符的字符串。|
|[NormalizeDiacritics](#normalizediacritics)|需要一个字符串参数。 返回字符串，但将任何标注字符替换为等效的非标注字符。|
|[Not](#not) |翻转 **source** 的布尔值。 如果 **source** 值为“*True*”，则返回“*False*”。 否则，返回“*True*”。| 
|[RemoveDuplicates](#removeduplicates)|RemoveDuplicates 函数使用多值字符串，并确保每个值都是唯一值。| 
|[替换](#replace) |替换字符串中的值。 | 
|[SelectUniqueValue](#selectuniquevalue)|需要至少两个参数，这些参数是使用表达式定义的唯一值生成规则。 此函数会评估每个规则，然后在目标应用/目录中检查生成的值的唯一性。| 
|[SingleAppRoleAssignment](#singleapproleassignment)|对于给定的应用程序，从向一个用户分配的所有 appRoleAssignments 列表中返回单个 appRoleAssignment。| 
|[Split](#split)|使用指定的分隔符字符，将字符串拆分为多值数组。|
|[StringFromSID](#stringfromsid)|StringFromSid 函数将包含安全标识符的字节数组转换为字符串。| 
|[StripSpaces](#stripspaces) |从源字符串中删除所有空格 (" ") 字符。| 
|[开关](#switch)|当 **source** 值匹配 **key** 时，会返回用于该 **key** 的 **value**。 | 
|[ToLower](#tolower)|采用源  字符串值并用指定的区域性规则将其转换为小写。| 
|[ToUpper](#toupper)|采用源  字符串值并用指定的区域性规则将其转换为大写。|
|[Trim](#trim)|Trim 函数从字符串中删除前导空格和尾随空格。|
|[Word](#word)|基于描述要使用的分隔符与要返回的单词数的参数，Word 函数返回字符串中包含的单词。|

---
### <a name="append"></a>附加
**函数：**<br> Append(source, suffix)

**说明：**<br> 获取源字符串值，并将后缀附加到其末尾。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |通常是来自源对象的属性的名称。 |
   | **suffix** |必选 |String |要附加到源值末尾的字符串。 |

---
### <a name="bitand"></a>BitAnd
**说明：**  
BitAnd 函数设置值的指定位。

**语法：**  
`num BitAnd(num value1, num value2)`

* value1、value2：应该使用 AND 联接在一起的数字值

**备注：**  
此函数将两个参数转换为二进制表示形式，并将位设置为：

* 0 - 如果 *value1* 和 *value2* 中的一个或两个对应位为 0
* 1 - 如果两个相应位均为 1。

换而言之，除了当两个参数的相应位均为 1 时之外，所有情况下均返回 0。

**示例：**  
 
 `BitAnd(&HF, &HF7)`</br>
 返回 7，因为十六进制 "F" AND "F7" 的计算结果为此值。

---

### <a name="cbool"></a>CBool
**说明：**  
CBool 函数基于计算的表达式返回布尔值

**语法：**  
`bool CBool(exp Expression)`

**备注：**  
如果表达式的计算结果为非零值，则 CBool 返回 True，否则返回 False。

**示例：**  
`CBool([attrib1] = [attrib2])`  

如果两个属性具有相同的值，则返回 True。

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**说明：**  
ConvertFromBase64 函数将指定的 base64 编码值转换为规则的字符串。

**语法：**  
`str ConvertFromBase64(str source)` - 假设采用 Unicode 编码  
`str ConvertFromBase64(str source, enum Encoding)`

* source：Base64 编码的字符串  
* Encoding：Unicode、ASCII、UTF8

**示例**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

这两个示例均返回 "*Hello world!* "

---
### <a name="converttobase64"></a>ConvertToBase64
**说明：**  
ConvertToBase64 函数将字符串转换为 Unicode base64 字符串。  
将整数数组的值转换为其等效字符串表示形式，该表示形式使用 base 64 数字编码。

**语法：**  
`str ConvertToBase64(str source)`

**示例：**  
`ConvertToBase64("Hello world!")`  
返回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**说明：**  
ConvertToUTF8Hex 函数将字符串转换为 UTF8 Hex 编码的值。

**语法：**  
`str ConvertToUTF8Hex(str source)`

**备注：**  
此函数的输出格式被 Azure Active Directory 用作 DN 属性。

**示例：**  
`ConvertToUTF8Hex("Hello world!")`  
返回 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**说明：**  
Count 函数返回多值属性中的元素数量

**语法：**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**说明：**  
CStr 函数转换为字符串数据类型。

**语法：**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value：可以是数字值、引用属性或布尔值。

**示例：**  
`CStr([dn])`  
可能返回 "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum
**说明：**  
DateFromNum 函数将 AD 的日期格式的值转换为 DateTime 类型。

**语法：**  
`dt DateFromNum(num value)`

**示例：**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
返回表示 2012-01-01 23:00:00 的 DateTime

---
### <a name="dncomponent"></a>DNComponent
**说明：**  
DNComponent 函数返回从左边起的指定 DN 组件的值。

**语法：**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn：要解释的引用属性
* ComponentNumber：DN 中要返回的组件

**示例：**  
`DNComponent(CRef([dn]),1)`  
如果 dn 为“cn=Joe,ou=…”，则返回 Joe

---
### <a name="error"></a>错误
**说明：**  
Error 函数用于返回自定义错误。

**语法：**  
`void Error(str ErrorMessage)`

**示例：**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
如果属性 accountName 不存在，则对象上引发错误。

---
### <a name="formatdatetime"></a>FormatDateTime
**函数：**<br> FormatDateTime(source, inputFormat, outputFormat)

**说明：**<br> 从一种格式获取日期字符串并将其转换为不同的格式。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |通常是来自源对象的属性的名称。 |
   | **inputFormat** |必选 |String |源值的预期格式。 有关支持的格式，请参阅 [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
   | **outputFormat** |必选 |String |输出日期的格式。 |

---
### <a name="guid"></a>Guid
**说明：**  
函数 GUID 生成新的随机 GUID

**语法：**  
`str Guid()`

---
### <a name="iif"></a>IIF
**说明：**  
IIF 函数基于指定的条件返回一组可能值中的其中一个值。

**语法：**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condition：计算结果可能为 true 或 false 的任何值或表达式。
* valueIfTrue：如果条件计算结果为 true，则为返回值。
* valueIfFalse：如果条件计算结果为 false，则为返回值。

**示例：**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 如果用户是实习生，则返回用户的别名，同时将 "t-" 添加到其开头，否则按原样返回用户的别名。

---
### <a name="instr"></a>InStr
**说明：**  
InStr 函数查找字符串中第一次出现的子字符串

**语法：**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck：要搜索的字符串
* stringmatch：要查找的字符串
* start：查找子字符串的起始位置
* compare：vbTextCompare 或 vbBinaryCompare

**备注：**  
返回其中已找到子字符串的位置，如果未找到，则返回 0。

**示例：**  
`InStr("The quick brown fox","quick")`  
计算结果为 5

`InStr("repEated","e",3,vbBinaryCompare)`  
计算结果为 7

---
### <a name="isnull"></a>IsNull
**说明：**  
如果表达式的计算结果为 Null，则 IsNull 函数返回 true。

**语法：**  
`bool IsNull(var Expression)`

**备注：**  
对于属性，Null 表示缺少属性。

**示例：**  
`IsNull([displayName])`  
如果属性没有在 CS 或 MV 中出现，则返回 True。

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**说明：**  
如果表达式为 null 或空字符串，则 IsNullOrEmpty 函数返回 true。

**语法：**  
`bool IsNullOrEmpty(var Expression)`

**备注：**  
对于属性，如果属性不存在，或存在但为空字符串，此语法计算结果则为 True。  
此函数的逆函数被命名为 IsPresent。

**示例：**  
`IsNullOrEmpty([displayName])`  
如果属性在 CS 或 MV 中没有出现或为空字符串，则返回 True。

---
### <a name="ispresent"></a>IsPresent
**说明：**  
如果表达式的计算结果为字符串，该字符串不是 Null 且不为空，则 IsPresent 函数返回 true。

**语法：**  
`bool IsPresent(var expression)`

**备注：**  
此函数的逆函数被命名为 IsNullOrEmpty。

**示例：**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**说明：**  
Item 函数返回多值字符串/属性中的一个项。

**语法：**  
`var Item(mvstr attribute, num index)`

* attribute：多值属性
* index：对多值字符串中某个项的索引。

**备注：**  
Item 函数与 Contains 函数一起使用很有利，因为后一函数返回对多值属性中某个项的索引。

如果索引超出界限，则引发错误。

**示例：**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
返回主电子邮件地址。

---
### <a name="isstring"></a>IsString
**说明：**  
如果表达式可以计算为字符串类型，则 IsString 函数计算结果为 True。

**语法：**  
`bool IsString(var expression)`

**备注：**  
用来确定 CStr() 是否能成功分析表达式。

---
### <a name="join"></a>Join
**函数：**<br> Join(separator, source1, source2, …)

**说明：**<br> Join() 类似于 Append()，只不过它可以将多个 **source** 字符串值组合到单个字符串中，且每个值由 **separator** 字符串分隔。

如果其中一个源值是多值属性，那么该属性中的每个值都将联接在一起，由分隔符值分隔。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | separator  |必选 |String |用于在将源值连接为一个字符串时分隔源值的字符串。 如果不需要分隔符，则可以是 ""。 |
   | **source1  … sourceN** |必选，次数可变 |String |要联接在一起的字符串值。 |

---
### <a name="left"></a>Left
**说明：**  
Left 函数从字符串左侧起返回指定的字符数。

**语法：**  
`str Left(str string, num NumChars)`

* string：从中返回字符的字符串
* NumChar：标识从字符串开头（左侧）起返回的字符数的数字

**备注：**  
包含字符串中第一个 numChars 字符的字符串：

* 如果 numChar = 0，则返回空字符串。
* 如果 numChar < 0，则返回输入字符串。
* 如果字符串为 null，则返回空字符串。

如果字符串包含的字符数比 numChars 中指定的数量少，则返回与该字符串相同的字符串（即，包含参数 1 中的所有字符）。

**示例：**  
`Left("John Doe", 3)`  
返回 `Joh`。

---
### <a name="mid"></a>Mid
**函数：**<br> Mid(source, start, length)

**说明：**<br> 返回源值的子字符串。 子字符串是一个只包含源字符串中某些字符的字符串。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |通常是属性的名称。 |
   | **start** |必选 |integer |**source** 字符串中的索引，子字符串应从此处开始。 字符串中第一个字符的索引为 1，第二个字符的索引为 2，依此类推。 |
   | **length** |必选 |integer |子字符串的长度。 如果长度超出 **source** 字符串，则函数将返回从 **start** 索引到 **source** 字符串末尾的子字符串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**函数：**<br> NormalizeDiacritics(source)

**说明：**<br> 需要一个字符串参数。 返回字符串，但将任何标注字符替换为等效的非标注字符。 通常用于将包含标注字符（重音符号）的名字和姓氏转换为可用于各种用户标识符（例如用户主体名称、SAM 帐户名称和电子邮件地址）的合法值。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String | 通常是名字或姓氏属性。 |

---
### <a name="not"></a>Not
**函数：**<br> Not(source)

**说明：**<br> 翻转 **source** 的布尔值。 如果 **source** 值为“*True*”，则返回“*False*”。 否则，返回“*True*”。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |布尔型字符串 |预期的 **source** 值为“True”或“False”。 |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**说明：**  
RemoveDuplicates 函数使用多值字符串，并确保每个值都是唯一值。

**语法：**  
`mvstr RemoveDuplicates(mvstr attribute)`

**示例：**  
`RemoveDuplicates([proxyAddresses])`  
返回净化的 proxyAddress 属性，其中所有重复值已被删除。

---
### <a name="replace"></a>将
**函数：**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**说明：**<br>
替换字符串中的值。 其工作方式取决于提供的参数：

* 当提供 **oldValue** 和 **replacementValue** 时：
  
  * 将 **source** 中出现的所有 **oldValue** 替换为 **replacementValue**
* 当提供 **oldValue** 和 **template** 时：
  
  * 将 **template** 中出现的所有 **oldValue** 都替换为 **source** 值
* 如果已提供 **regexPattern** 和 **replacementValue**：

  * 函数将 **regexPattern** 应用到 **source** 字符串，你可以使用 regex 组名称来构造 **replacementValue** 的字符串。
* 当提供 **regexPattern**、**regexGroupName**、**replacementValue** 时：
  
  * 函数将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为 **replacementValue**
* 如果已提供 **regexPattern**、**regexGroupName** 和 **replacementAttributeName**：
  
  * 如果 **source** 没有值，则返回 **source**
  * 如果 **source** 具有值，则函数会将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为与 **replacementAttributeName** 关联的值

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |通常是 **source** 对象中的属性的名称。 |
   | **oldValue** |可选 |String |要在 **source** 或 **template** 中替换的值。 |
   | **regexPattern** |可选 |String |要在 **source** 中替换的值的正则表达式模式。 或者，当使用 **replacementPropertyName** 时，从 **replacementPropertyName** 中提取值的模式。 |
   | **regexGroupName** |可选 |String |**regexPattern** 中的组名称。 仅当使用 **replacementPropertyName** 时，才会从 **replacementPropertyName** 中以 **replacementValue** 的形式提取此组的值。 |
   | **replacementValue** |可选 |String |用于替换旧值的新值。 |
   | **replacementAttributeName** |可选 |String |用于替换值的属性的名称 |
   | **template** |可选 |String |当提供 **template** 值时，会在模板中查找 **oldValue** 并将其替换为 **source** 值。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**函数：**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**说明：**<br> 需要至少两个参数，这些参数是使用表达式定义的唯一值生成规则。 此函数会评估每个规则，然后在目标应用/目录中检查生成的值的唯一性。 将返回找到的第一个唯一值。 如果所有值都已存在于目标中，则会托管该条目并在审核日志中记录原因。 可以提供的参数数目没有上限。

> [!NOTE]
> - 这是一个顶级函数，不能嵌套。
> - 此函数不能应用到具有匹配优先级的属性。  
> - 此函数仅供用于创建条目。 将其与属性一起使用时，请将“应用映射”  属性设置为“仅在创建对象期间”。 
> - 目前只有“Workday 到 Active Directory 的用户预配”支持此函数。 此函数不可用于其他预配应用程序。 


**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **uniqueValueRule1  … uniqueValueRuleN** |需要至少 2 个，没有上限 |String | 要评估的唯一值生成规则的列表。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**函数：**<br> SingleAppRoleAssignment([appRoleAssignments])

**说明：**<br> 对于给定的应用程序，从向一个用户分配的所有 appRoleAssignments 列表中返回单个 appRoleAssignment。 需要此函数才能将 appRoleAssignments 对象转换为单个角色名称字符串。 请注意，最佳做法是确保每次只向一个用户分配一个 appRoleAssignment，如果分配了多个角色，则返回的角色字符串可能是不可预测的。 

**参数：**<br> 

  | 名称 | 必选/重复 | 类型 | 说明 |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |必选 |String |**[appRoleAssignments]** 对象。 |

---
### <a name="split"></a>拆分
**函数：**<br> Split(source, delimiter)

**说明：**<br> 使用指定的分隔符字符，将字符串拆分为多值数组。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |要更新的 **source** 值。 |
   | **delimiter** |必选 |String |指定将用来拆分字符串的字符（示例：“,”） |

---
### <a name="stringfromsid"></a>StringFromSid
**说明：**  
StringFromSid 函数将包含安全标识符的字节数组转换为字符串。

**语法：**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**函数：**<br> StripSpaces(source)

**说明：**<br> 从源字符串中删除所有空格 (" ") 字符。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |要更新的 **source** 值。 |

---
### <a name="switch"></a>开关
**函数：**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**说明：**<br> 当 **source** 值匹配 **key** 时，会返回用于该 **key** 的 **value**。 如果 **source** 值不匹配任何键，则返回 **defaultValue**。  **Key** 和 **value** 参数必须始终成对出现。 该函数始终需要偶数个参数。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |要检查的 **Source** 值。 |
   | **defaultValue** |可选 |String |当 source 不匹配任何 key 时使用的默认值。 可以是空字符串 ("")。 |
   | **键** |必选 |String |用来比较 **source** 值的 **key**。 |
   | **value** |必选 |String |与该 key 匹配的 **source** 的替换值。 |

---
### <a name="tolower"></a>ToLower
**函数：**<br> ToLowe（源、区域性）

**说明：**<br> 采用源  字符串值并用指定的区域性规则将其转换为小写。 如果没有指定任何区域性  信息，则使用固定区域性。

**参数：**<br> 

   | 名称 | 必选/重复 | 类型 | 说明 |
   | --- | --- | --- | --- |
   | **源 (source)** |必选 |String |通常是来自源对象的属性的名称 |
   | **区域性** |可选 |String |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2  ，其中 languagecode2  是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码  。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---

### <a name="toupper"></a>ToUpper
**函数：**<br> ToUpper（源、区域性）

**说明：**<br> 采用源  字符串值并用指定的区域性规则将其转换为大写。 如果没有指定任何区域性  信息，则使用固定区域性。

**参数：**<br> 

  | 名称 | 必选/重复 | 类型 | 说明 |
  | --- | --- | --- | --- |
  | **源 (source)** |必选 |String |通常是来自源对象的属性的名称。 |
  | **区域性** |可选 |String |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2  ，其中 languagecode2  是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码  。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---

### <a name="trim"></a>Trim
**说明：**  
Trim 函数从字符串中删除前导空格和尾随空格。

**语法：**  
`str Trim(str value)`  

**示例：**  
`Trim(" Test ")`  
返回“Test”。

`Trim([proxyAddresses])`  
删除 proxyAddress 属性中每个值的前导空格和尾随空格。

---
### <a name="word"></a>Word
**说明：**  
基于描述要使用的分隔符与要返回的单词数的参数，Word 函数返回字符串中包含的单词。

**语法：**  
`str Word(str string, num WordNumber, str delimiters)`

* string：从中返回单词的字符串。
* WordNumber：标识应返回单词数的数字。
* delimiter：表示应该用于标识单词的分隔符的字符串

**备注：**  
字符串中的字符由分隔符中其中一个字符分隔的每个字符串被标识为单词：

* 如果数字 < 1，则返回空字符串。
* 如果字符串为 null，则返回空字符串。

如果字符串包含的单词少于应返回数字或字符串不包含由分隔符标识的任何单词，则返回空字符串。

**示例：**  
`Word("The quick brown fox",3," ")`  
返回“brown”

`Word("This,string!has&many separators",3,",!&#")`  
返回“has”

## <a name="examples"></a>示例
### <a name="strip-known-domain-name"></a>删除已知域名
需要从用户的电子邮件中删除已知域名，以便获取用户名。 <br>
例如，如果域为“contoso.com”，则可以使用以下表达式：

**表达式：** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**示例输入/输出：** <br>

* **输入** (mail)：“john.doe@contoso.com”
* **输出**：“john.doe”

### <a name="append-constant-suffix-to-user-name"></a>将常量后缀附加到用户名
如果使用 Salesforce 沙箱，则可能需要在进行同步之前向所有用户名附加额外后缀。

**表达式：** <br>
`Append([userPrincipalName], ".test")`

**示例输入/输出：** <br>

* **输入**：(userPrincipalName)：“John.Doe@contoso.com”
* **输出**：“John.Doe@contoso.com.test”

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>通过连接名字和姓氏部分来生成用户别名
需要使用用户名字的前 3 个字母和用户姓氏的前 5 个字母来生成用户别名。

**表达式：** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**示例输入/输出：** <br>

* **输入** (givenName)："John"
* **输入** (surname)："Doe"
* **输出**："JohDoe"

### <a name="remove-diacritics-from-a-string"></a>从字符串中删除音调符号
需要将包含重音符号的字符替换为不包含重音符号的等效字符。

**表达式：** <br>
NormalizeDiacritics([givenName])

**示例输入/输出：** <br>

* **输入** (givenName)："Zoë"
* **输出**："Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>将字符串拆分为多值数组
你需要获取一个以逗号分隔的字符串列表，将它们拆分为一个数组，可以将该数组插入到多值属性中，例如 Salesforce 的 PermissionSets 属性。 在此示例中，Azure AD 中的 extensionAttribute5 中填充了一个权限集列表。

**表达式：** <br>
Split([extensionAttribute5], ",")

**示例输入/输出：** <br>

* **INPUT** (extensionAttribute5):"PermissionSetOne, PermisionSetTwo"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>输出日期是一种特定格式的字符串
需要以某种格式将日期发送到 SaaS 应用程序。 <br>
例如，需要为 ServiceNow 设置日期格式。

**表达式：** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**示例输入/输出：**

* **输入** (extensionAttribute1)："20150123105347.1Z"
* **输出**："2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根据预定义的选项集替换值

需要根据存储在 Azure AD 中的状态代码来定义用户的时区。 <br>
如果状态代码与任何预定义选项都不匹配，则使用默认值“澳大利亚/悉尼”。

**表达式：** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**示例输入/输出：**

* **输入** (state)："QLD"
* **输出**："Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>使用正则表达式替换字符
你需要查找与正则表达式匹配的值并将其删除。

**表达式：** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**示例输入/输出：**

* **INPUT** (mailNickname: "john_doe72"
* **输出**："72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>将生成的 userPrincipalName (UPN) 值转换为小写
在下面的示例中，通过串联 PreferredFirstName 和 PreferredLastName 源字段生成 UPN 值，ToLower 函数对生成的字符串进行运算，将所有字符都转换为小写形式。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**：“john.smith@contoso.com”

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>为 userPrincipalName (UPN) 属性生成唯一值
你需要根据用户的名字、中间名和姓氏为 UPN 属性生成值，并在将该值分配给 UPN 属性之前在目标 AD 目录中检查其唯一性。

**表达式：** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**："John.Smith@contoso.com"，如果 UPN 值 John.Smith@contoso.com 尚未存在于目录中
* **输出**："J.Smith@contoso.com"，如果 UPN 值 John.Smith@contoso.com 已存在于目录中
* **输出**："Jo.Smith@contoso.com"，如果上面的两个值已存在于目录中


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
