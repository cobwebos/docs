---
title: 编写 Azure AD 中的属性映射的表达式
description: 了解如何在 Azure Active Directory 中自动预配 SaaS 应用对象期间，使用表达式映射将属性值转换为可接受的格式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc41a18063202bfefb9ddf7238de17fc691984af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612141"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>在 Azure Active Directory 中编写属性映射的表达式
将预配配置到 SaaS 应用程序时，表达式映射是可指定的属性映射类型之一。 为此，必须编写一个类似于脚本的表达式，允许将用户的数据转换为 SaaS 应用程序更可接受的格式。

## <a name="syntax-overview"></a>语法概述
属性映射的表达式语法让人联想到 Visual Basic for Applications (VBA) 函数。

* 必须定义整个表达式的函数，函数中的名称后跟带括号的参数： <br>
  *函数名称（`<<argument 1>>``<<argument N>>`*
* 函数之间可以相互嵌套。 例如： <br> *功能一（功能二））`<<argument1>>`*
* 可以将三种不同类型的参数传递给函数：
  
  1. 属性，必须括在方括号中。 例如：[attributeName]
  2. 字符串常量必须括在双引号内。 例如："美国"
  3. 其他函数。 例如：函数一（，`<<argument1>>`功能二））`<<argument2>>`
* 对于字符串常量，如果字符串中需要反斜杠 ( \ ) 或引号 ( " )，则必须使用反斜杠 ( \ ) 符号进行转义。 例如："公司名称："Contoso"" \\\\

## <a name="list-of-functions"></a>函数列表
[附属](#append)&nbsp;&nbsp;[Count](#count)[Coalesce](#coalesce)&nbsp;[BitAnd](#bitand)&nbsp;&nbsp;[CStr](#cstr)[CBool](#cbool)&nbsp;&nbsp;[ConvertToBase64](#converttobase64)&nbsp;[ConvertToUTF8Hex](#converttoutf8hex)&nbsp;位和&nbsp;&nbsp;CBool&nbsp;&nbsp;组合&nbsp;转换到&nbsp;Base64&nbsp;转换到UTF8Hex&nbsp;计数 Cstr &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;[Guid](#guid)&nbsp;[DateFromNum](#datefromnum)&nbsp;&nbsp;[IIF](#iif)&nbsp;[InStr](#instr)&nbsp;[IsNull](#isnull)[FormatDateTime](#formatdatetime)&nbsp;&nbsp;&nbsp;&nbsp;日期&nbsp;从num&nbsp;&nbsp;[IsNullOrEmpty](#isnullorempty)格式日期吉德&nbsp;&nbsp;IIF &nbsp; instr &nbsp; isnull 是nullor空&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[是存在](#ispresent)&nbsp;&nbsp;&nbsp;[Mid](#mid)[Join](#join)[Left](#left)[Not](#not)[Item](#item)[IsString](#isstring)&nbsp;[NormalizeDiacritics](#normalizediacritics)是字符串&nbsp;项目加入左中&nbsp;规范化符号不&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Replace](#replace)&nbsp;[Split](#split)[SingleAppRoleAssignment](#singleapproleassignment)[RemoveDuplicates](#removeduplicates)&nbsp;[SelectUniqueValue](#selectuniquevalue)删除重复替换&nbsp;&nbsp;选择唯&nbsp;一价值&nbsp;单应用程序分配拆分&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[条带空格](#stripspaces)&nbsp;&nbsp;&nbsp;&nbsp;[Switch](#switch)&nbsp;[ToLower](#tolower)[Word](#word)[ToUpper](#toupper)切换到&nbsp;"下&nbsp;到&nbsp;上字"&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;

---
### <a name="append"></a>附加
**功能：**<br> Append(source, suffix)

**描述:**<br> 获取源字符串值，并将后缀附加到其末尾。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |通常是来自源对象的属性的名称。 |
| **suffix** |必选 |String |要附加到源值末尾的字符串。 |

---
### <a name="bitand"></a>BitAnd
**功能：**<br> 位和（值 1，值 2）

**描述:**<br> 此函数将两个参数转换为二进制表示形式，并将位设置为：

0 - 如果 value1 和 value2 中的一个或两个对应位为 0                                                  
1 - 如果两个相应位均为 1。                                    

换而言之，除了当两个参数的相应位均为 1 时之外，所有情况下均返回 0。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **值1** |必选 |num |应使用值 2 的数值|
| **值2** |必选 |num |应使用值 1 的 AND 的数值|

**例子：**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 和 0000011 = 00000111 = 00000111 使 BitAnd 返回 7，二进制值 00000111

---
### <a name="cbool"></a>CBool
**功能：**<br> CBool（表达）

**描述:**<br> CBool 返回基于计算表达式的布尔。 如果表达式计算为非零值，则 CBool 返回 True，否则返回 False。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必选 | 表达式 | 任何有效的表达式 |

**例子：**<br>
CBool（[属性1]=[属性2]）                                                                    
如果两个属性具有相同的值，则返回 True。

---
### <a name="coalesce"></a>Coalesce
**功能：**<br> 合并（源 1，源 2，...，默认值）

**描述:**<br> 返回第一个不是 NULL 的源值。 如果所有参数都为 NULL，并且存在默认值，则将返回默认值。 如果所有参数都是 NULL，并且不存在默认值，则合并返回 NULL。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源1 ...源N** | 必选 | String |必需的可变次数。 通常是来自源对象的属性的名称。 |
| **defaultValue** | 可选 | String | 所有源值为 NULL 时使用的默认值。 可以是空字符串 ("")。

---
### <a name="converttobase64"></a>ConvertToBase64
**功能：**<br> 转换到Base64（源）

**描述:**<br> ConvertToBase64 函数将字符串转换为 Unicode base64 字符串。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |要转换为基 64 的字符串|

**例子：**<br>
ConvertToBase64("Hello world!")                                                                                                        
返回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**功能：**<br> 转换到UTF8Hex（源）

**描述:**<br> ConvertToUTF8Hex 函数将字符串转换为 UTF8 Hex 编码的值。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |要转换为 UTF8 十六进制的字符串|

**例子：**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
返回 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**功能：**<br> 计数（属性）

**描述:**<br> Count 函数返回多值属性中的元素数量

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **属性** |必选 |属性 |多值属性，将元素计数|

---
### <a name="cstr"></a>CStr
**功能：**<br> CStr（值）

**描述:**<br> CStr 函数将值转换为字符串数据类型。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **value** |必选 | 数值、参考或布尔 | 可以是数字值、引用属性或布尔值。 |

**例子：**<br>
CStr([dn])                                                            
返回"cn=Joe，dc_contoso，dc_com"

---
### <a name="datefromnum"></a>DateFromNum
**功能：**<br> 日期从Num（值）

**描述:**<br> DateFromNum 函数将 AD 的日期格式的值转换为 DateTime 类型。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **value** |必选 | Date | 要转换为日期时间类型的 AD 日期 |

**例子：**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
返回表示 2012-01-01 23:00:00 的 DateTime

---
### <a name="formatdatetime"></a>FormatDateTime
**功能：**<br> FormatDateTime(source, inputFormat, outputFormat)

**描述:**<br> 从一种格式获取日期字符串并将其转换为不同的格式。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |通常是来自源对象的属性的名称。 |
| **inputFormat** |必选 |String |源值的预期格式。 有关支持的格式，请参阅[https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
| **输出格式** |必选 |String |输出日期的格式。 |

---
### <a name="guid"></a>Guid
**功能：**<br> Guid()

**描述:**<br> 函数 GUID 生成新的随机 GUID

---
### <a name="iif"></a>IIF
**功能：**<br> IIF（条件，值如果真，值如果假）

**描述:**<br> IIF 函数基于指定的条件返回一组可能值中的其中一个值。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **条件** |必选 |变量或表达式 |计算结果为 true 或 false 的任何值或表达式。 |
| **valueIfTrue** |必选 |变量或字符串 | 如果条件计算结果为 true，则为返回值。 |
| **valueIfFalse** |必选 |变量或字符串 |如果条件计算结果为 false，则为返回值。|

**例子：**<br>
IIF（[国家]"美国"，"国家"，[部门]）

---
### <a name="instr"></a>InStr
**功能：**<br> InStr（值 1，值 2，开始，比较类型）

**描述:**<br> InStr 函数查找字符串中第一次出现的子字符串

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **值1** |必选 |String |要搜索的字符串 |
| **值2** |必选 |String |要找到的字符串 |
| **开始** |可选 |Integer |起始位置以查找子字符串|
| **比较类型** |可选 |枚举 |可以是 vb 文本比较或 vbBinary 比较 |

**例子：**<br>
InStr("The quick brown fox","quick")                                                                             
计算结果为 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
计算结果为 7

---
### <a name="isnull"></a>IsNull
**功能：**<br> IsNull（表达式）

**描述:**<br> 如果表达式的计算结果为 Null，则 IsNull 函数返回 true。 对于属性，Null 表示缺少属性。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必选 |表达式 |要计算的表达式 |

**例子：**<br>
IsNull([displayName])                                                                                                
如果属性不存在，则返回 True

---
### <a name="isnullorempty"></a>是空的
**功能：**<br> 空为（表达式）

**描述:**<br> 如果表达式为 null 或空字符串，则 IsNullOrEmpty 函数返回 true。 对于属性，如果属性不存在，或存在但为空字符串，此语法计算结果则为 True。
此函数的逆函数被命名为 IsPresent。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必选 |表达式 |要计算的表达式 |

**例子：**<br>
是空（[显示名称]）                                               
如果属性不存在或为空字符串，则返回 True

---
### <a name="ispresent"></a>IsPresent
**功能：**<br> 存在（表达式）

**描述:**<br> 如果表达式的计算结果为字符串，该字符串不是 Null 且不为空，则 IsPresent 函数返回 true。 此函数的逆函数被命名为 IsNullOrEmpty。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必选 |表达式 |要计算的表达式 |

**例子：**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**功能：**<br> 正弦（表达式）

**描述:**<br> 如果表达式可以计算为字符串类型，则 IsString 函数计算结果为 True。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **expression** |必选 |表达式 |要计算的表达式 |

---
### <a name="item"></a>Item
**功能：**<br> 项目（属性、索引）

**描述:**<br> Item 函数返回多值字符串/属性中的一个项。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **属性** |必选 |特性 |要搜索的多值属性 |
| **index** |必选 |Integer | 索引到多值字符串中的项|

**例子：**<br>
项目（[代理地址]，1）

---
### <a name="join"></a>联接
**功能：**<br> Join(separator, source1, source2, …)

**描述:**<br> Join() 类似于 Append()，不同的是，它可以将多个 **source** 字符串值组合成单个字符串，每个值将由 **separator** 字符串分隔。

如果其中一个源值是多值属性，那么该属性中的每个值都将联接在一起，由分隔符值分隔。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **分离** |必选 |String |用于在将源值连接为一个字符串时分隔源值的字符串。 如果不需要分隔符，则可以是 ""。 |
| **源1 ...源N** |必选，次数可变 |String |要联接在一起的字符串值。 |

---
### <a name="left"></a>Left
**功能：**<br> 左侧（弦，串）

**描述:**<br> Left 函数从字符串左侧起返回指定的字符数。 如果 numChar = 0，则返回空字符串。
如果 numChar < 0，则返回输入字符串。
如果字符串为 null，则返回空字符串。
如果字符串包含的字符数比 numChars 中指定的数量少，则返回与该字符串相同的字符串（即，包含参数 1 中的所有字符）。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **字符串** |必选 |特性 | 要从 中返回字符的字符串 |
| **纳姆查尔斯** |必选 |Integer | 标识从字符串开头（左）返回的字符数的数字|

**例子：**<br>
左图（"约翰·多伊"，3）                                                            
返回"Joh"

---
### <a name="mid"></a>Mid
**功能：**<br> Mid(source, start, length)

**描述:**<br> 返回源值的子字符串。 子字符串是一个只包含源字符串中某些字符的字符串。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |通常是属性的名称。 |
| **开始** |必选 |integer |**source** 字符串中的索引，子字符串应从这里开始。 字符串中第一个字符的索引为 1，第二个字符的索引为 2，依此类推。 |
| **length** |必选 |integer |子字符串的长度。 如果长度超出 **source** 字符串，则函数将返回从 **start** 索引到 **source** 字符串末尾的子字符串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**功能：**<br> NormalizeDiacritics(source)

**描述:**<br> 需要一个字符串参数。 返回字符串，但将任何标注字符替换为等效的非标注字符。 通常用于将包含标注字符（重音符号）的名字和姓氏转换为可用于各种用户标识符（例如用户主体名称、SAM 帐户名称和电子邮件地址）的合法值。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String | 通常是名字或姓氏属性。 |

---
### <a name="not"></a>Not
**功能：**<br> Not(source)

**描述:**<br> 对 **source** 的布尔值取反。 如果 **source** 值为 *True*，则返回 *False*。 反之，则返回 *True*。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |布尔型字符串 |预期的 **source** 值为“True”或“False”。 |

---
### <a name="numfromdate"></a>NumFromDate
**功能：**<br> 数字从日期（值）

**描述:**<br> NumFromDate 函数将 DateTime 值转换为活动目录格式，该格式是设置[帐户过期](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires)等属性所必需的。 使用此函数可将从云 HR 应用（如工作日和成功因素）收到的 DateTime 值转换为其等效的 AD 表示形式。 

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **value** |必选 | String | 受支持格式的日期时间字符串。 有关支持的格式，请参阅 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx。 |

**例子：**<br>
* 工作日示例 <br>
  假设您要将属性*ContractEndDate*从工作日（以格式*为 2020-12-31-08：00*映射到 AD 中的*帐户过期*字段）进行映射，下面介绍如何使用此函数并更改时区偏移以匹配区域设置。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* 成功因素示例 <br>
  假设您要映射属性*endDate*从成功因子这是在格式*M/d/yyyyhhhhh：ss tt*帐户*过期*字段在 AD 中， 下面是如何使用此函数和更改时区偏移以匹配您的区域设置.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**功能：**<br> 删除重复项（属性）

**描述:**<br> RemoveDuplicates 函数使用多值字符串，并确保每个值都是唯一值。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **属性** |必选 |多值属性 |将删除重复项的多值属性|

**例子：**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
返回已删除所有重复值的已清理代理地址属性

---
### <a name="replace"></a>将
**功能：**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**描述:**<br>
替换字符串中的值。 其工作方式取决于提供的参数：

* 当提供了 **oldValue** 和 **replacementValue** 时：
  
  * 将**源**中**所有旧值**事件替换为**替换值**
* 当提供了 **oldValue** 和 **template** 时：
  
  * 将 **template** 中出现的所有 **oldValue** 替换为 **source** 值
* 如果已提供 **regexPattern** 和 **replacementValue**：

  * 函数将 **regexPattern** 应用到 **source** 字符串，你可以使用 regex 组名称来构造 **replacementValue** 的字符串。
* 当提供 **regexPattern**、**regexGroupName**、**replacementValue** 时：
  
  * 函数将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为 **replacementValue**
* 如果已提供 **regexPattern**、**regexGroupName** 和 **replacementAttributeName**：
  
  * 如果 **source** 没有值，则返回 **source**
  * 如果 **source** 具有值，则函数会将 **regexPattern** 应用到 **source** 字符串，并将与 **regexGroupName** 匹配的所有值替换为与 **replacementAttributeName** 关联的值

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |通常来自**源**对象的属性的名称。 |
| **oldValue** |可选 |String |要在 **source** 或 **template** 中替换的值。 |
| **regexPattern** |可选 |String |**source** 中要替换的值的正则表达式模式。 或者，当使用 **replacementPropertyName** 时，从 **replacementPropertyName** 中提取值的模式。 |
| **regexGroupName** |可选 |String |**regexPattern** 中的组名称。 仅当使用 **replacementPropertyName** 时，才会从 **replacementPropertyName** 中以 **replacementValue** 的形式提取此组的值。 |
| **replacementValue** |可选 |String |用于替换旧值的新值。 |
| **replacementAttributeName** |可选 |String |用于替换值的属性的名称 |
| **模板** |可选 |String |当提供**模板**值时，我们将在模板中查找**旧值**并将其替换为**源**值。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**功能：**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**描述:**<br> 需要至少两个参数，这些参数是使用表达式定义的唯一值生成规则。 此函数会评估每个规则，然后在目标应用/目录中检查生成的值的唯一性。 将返回找到的第一个唯一值。 如果所有值都已存在于目标中，则会托管该条目并在审核日志中记录原因。 可以提供的参数数目没有上限。

> [!NOTE]
> - 这是一个顶级函数，不能嵌套。
> - 此函数不能应用到具有匹配优先级的属性。  
> - 此函数仅供用于创建条目。 将其与属性一起使用时，请将“应用映射”**** 属性设置为“仅在创建对象期间”。****
> - 目前只有“Workday 到 Active Directory 的用户预配”支持此函数。 此函数不可用于其他预配应用程序。 


**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |需要至少 2 个，没有上限 |String | 要评估的唯一值生成规则的列表。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**功能：**<br> SingleAppRoleAssignment([appRoleAssignments])

**描述:**<br> 对于给定的应用程序，从向一个用户分配的所有 appRoleAssignments 列表中返回单个 appRoleAssignment。 需要此函数才能将 appRoleAssignments 对象转换为单个角色名称字符串。 请注意，最佳做法是确保每次只向一个用户分配一个 appRoleAssignment，如果分配了多个角色，则返回的角色字符串可能是不可预测的。 

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必选 |String |**[appRoleAssignments]** 对象。 |

---
### <a name="split"></a>拆分
**功能：**<br> Split(source, delimiter)

**描述:**<br> 使用指定的分隔符字符，将字符串拆分为多值数组。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |要更新的 **source** 值。 |
| **分隔符** |必选 |String |指定将用来拆分字符串的字符（示例：“,”） |

---
### <a name="stripspaces"></a>StripSpaces
**功能：**<br> StripSpaces(source)

**描述:**<br> 从源字符串中删除所有空格 (" ") 字符。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |要更新的 **source** 值。 |

---
### <a name="switch"></a>开关
**功能：**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**描述:**<br> 当 **source** 值与一个 **key** 匹配时，返回该 **key** 所对应的 **value**。 当 **source** 值未与任何 key 匹配时，则返回 **defaultValue**。  **Key** 和 **value** 参数必须始终成对出现。 该函数始终需要偶数个参数。 该函数不应用于引用属性（如管理器）。 

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |要更新的 **source** 值。 |
| **defaultValue** |可选 |String |当 source 不匹配任何 key 时使用的默认值。 可以是空字符串 ("")。 |
| **关键** |必选 |String |用来比较 **source** 值的 **key**。 |
| **value** |必选 |String |与该 key 匹配的 **source** 的替换值。 |

---
### <a name="tolower"></a>ToLower
**功能：**<br> ToLowe（源、区域性）

**描述:**<br> 采用源** 字符串值并用指定的区域性规则将其转换为小写。 如果没有指定任何区域性** 信息，则使用固定区域性。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |通常是来自源对象的属性的名称 |
| **文化** |可选 |String |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2**，其中 languagecode2** 是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码**。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---
### <a name="toupper"></a>ToUpper
**功能：**<br> ToUpper（源、区域性）

**描述:**<br> 采用源** 字符串值并用指定的区域性规则将其转换为大写。 如果没有指定任何区域性** 信息，则使用固定区域性。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **源** |必选 |String |通常是来自源对象的属性的名称。 |
| **文化** |可选 |String |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2**，其中 languagecode2** 是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码**。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---
### <a name="word"></a>Word
**功能：**<br> 单词（字符串、字数、分隔符）

**描述:**<br> 基于描述要使用的分隔符与要返回的单词数的参数，Word 函数返回字符串中包含的单词。 字符串中的字符由分隔符中其中一个字符分隔的每个字符串被标识为单词：

如果数字 < 1，则返回空字符串。
如果字符串为 null，则返回空字符串。
如果字符串包含的单词少于应返回数字或字符串不包含由分隔符标识的任何单词，则返回空字符串。

**参数：**<br> 

| “属性” | 必选/重复 | 类型 | 说明 |
| --- | --- | --- | --- |
| **字符串** |必选 |多值属性 |要返回单词的字符串。|
| **字数** |必选 | Integer | 标识应返回的字号的数字|
| **分隔符** |必选 |String| 表示应用于标识单词的分隔符的字符串|

**例子：**<br>
字（"快速棕色狐狸"，3，""）                                                                                       
返回“brown”

单词（"这个，字符串！&许多分隔符"，"3"，！&"）                                                                       
返回"有"

---

## <a name="examples"></a>示例
### <a name="strip-known-domain-name"></a>删除已知域名
需要从用户的电子邮件中删除已知域名，以便获取用户名。 <br>
例如，如果域为“contoso.com”，则可以使用以下表达式：

**表达：** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**示例输入/输出：** <br>

* **输入** (mail)：“john.doe@contoso.com”
* **输出**：“john.doe”

### <a name="append-constant-suffix-to-user-name"></a>将常量后缀附加到用户名
如果使用 Salesforce 沙箱，则可能需要在进行同步之前向所有用户名附加额外后缀。

**表达：** <br>
`Append([userPrincipalName], ".test")`

**示例输入/输出：** <br>

* **输入**：(userPrincipalName)：“John.Doe@contoso.com”
* **输出**："John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>通过连接名字和姓氏部分来生成用户别名
需要使用用户名字的前 3 个字母和用户姓氏的前 5 个字母来生成用户别名。

**表达：** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**示例输入/输出：** <br>

* **输入** (givenName)：“John”
* **输入** (surname)：“Doe”
* **输出**：“JohDoe”

### <a name="remove-diacritics-from-a-string"></a>从字符串中删除音调符号
需要将包含重音符号的字符替换为不包含重音符号的等效字符。

**表达：** <br>
NormalizeDiacritics([givenName])

**示例输入/输出：** <br>

* **输入** (givenName)：“Zoë”
* **输出**：" 佐伊"

### <a name="split-a-string-into-a-multi-valued-array"></a>将字符串拆分为多值数组
你需要获取一个以逗号分隔的字符串列表，将它们拆分为一个数组，可以将该数组插入到多值属性中，例如 Salesforce 的 PermissionSets 属性。 在此示例中，Azure AD 中的 extensionAttribute5 中填充了一个权限集列表。

**表达：** <br>
Split([extensionAttribute5], ",")

**示例输入/输出：** <br>

* **输入**（扩展属性5）："权限集一，渗透集二"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>输出日期是一种特定格式的字符串
需要以某种格式将日期发送到 SaaS 应用程序。 <br>
例如，需要为 ServiceNow 设置日期格式。

**表达：** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**示例输入/输出：**

* **输入** (extensionAttribute1)：“20150123105347.1Z”
* **输出**：“2015-01-23”

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根据预定义的选项集替换值

需要根据存储在 Azure AD 中的状态代码来定义用户的时区。 <br>
如果状态代码与任何预定义选项都不匹配，则使用默认值“澳大利亚/悉尼”。

**表达：** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**示例输入/输出：**

* **输入** (state)：“QLD”
* **输出**：“澳大利亚/布里斯班”

### <a name="replace-characters-using-a-regular-expression"></a>使用正则表达式替换字符
你需要查找与正则表达式匹配的值并将其删除。

**表达：** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**示例输入/输出：**

* **INPUT** (mailNickname: "john_doe72"
* **输出**：" "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>将生成的 userPrincipalName (UPN) 值转换为小写
在下面的示例中，通过串联 PreferredFirstName 和 PreferredLastName 源字段生成 UPN 值，ToLower 函数对生成的字符串进行运算，将所有字符都转换为小写形式。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**："john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>为 userPrincipalName (UPN) 属性生成唯一值
你需要根据用户的名字、中间名和姓氏为 UPN 属性生成值，并在将该值分配给 UPN 属性之前在目标 AD 目录中检查其唯一性。

**表达：** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**："John.Smith@contoso.com如果 目录中不存在 的John.Smith@contoso.comUPN 值
* **输出**："J.Smith@contoso.com如果目录中已存在 的John.Smith@contoso.comUPN 值
* **输出**Jo.Smith@contoso.com：""如果目录中已存在上述两个 UPN 值

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>流邮件值（如果不是 NULL），否则流用户主名称
如果邮件属性存在，则希望将其流式传递。 如果不是，您希望改为流用户主体名称的值。

**表达：** <br>
`Coalesce([mail],[userPrincipalName])`

**示例输入/输出：** <br>

* **输入**（邮件）：空
* **输入**（用户主名）：John.Doe@contoso.com"
* **输出**："John.Doe@contoso.com"

## <a name="related-articles"></a>相关文章
* [在 SaaS 应用中自动预配和取消预配用户](../app-provisioning/user-provisioning.md)
* [为用户预配自定义属性映射](../app-provisioning/customize-application-attributes.md)
* [用于用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [帐户预配通知](../app-provisioning/user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
