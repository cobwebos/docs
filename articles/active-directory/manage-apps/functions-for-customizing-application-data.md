---
title: 在 Azure Active Directory 中编写属性映射的表达式 | Microsoft Docs
description: 了解如何在 Azure Active Directory 中自动预配 SaaS 应用对象期间，使用表达式映射将属性值转换为可接受的格式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12b75c2df7d11b0e90c5dccc3bc2aae4e0fb0c1e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204472"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>在 Azure Active Directory 中编写属性映射的表达式
将预配配置到 SaaS 应用程序时，表达式映射是可指定的属性映射类型之一。 为此，必须编写一个类似于脚本的表达式，允许将用户的数据转换为 SaaS 应用程序更可接受的格式。

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
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>附加
**函数：**<br> Append(source, suffix)

**说明：**<br> 获取源字符串值，并将后缀附加到其末尾。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |通常是来自源对象的属性的名称。 |
| **suffix** |必选 |String |要附加到源值末尾的字符串。 |

---
### <a name="formatdatetime"></a>FormatDateTime
**函数：**<br> FormatDateTime(source, inputFormat, outputFormat)

**说明：**<br> 从一种格式获取日期字符串并将其转换为不同的格式。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |通常是来自源对象的属性的名称。 |
| **inputFormat** |必选 |String |源值的预期格式。 有关支持的格式，请参阅 [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
| **outputFormat** |必选 |String |输出日期的格式。 |

---
### <a name="join"></a>Join
**函数：**<br> Join(separator, source1, source2, …)

**说明：**<br> Join() 类似于 Append()，只不过它可以将多个 **source** 字符串值组合到单个字符串中，且每个值由 **separator** 字符串分隔。

如果其中一个源值是多值属性，那么该属性中的每个值都将联接在一起，由分隔符值分隔。

**参数：**<br> 

| 名称 | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **separator** |必选 |String |用于在将源值连接为一个字符串时分隔源值的字符串。 如果不需要分隔符，则可以是 ""。 |
| **source1  … sourceN** |必选，次数可变 |String |要联接在一起的字符串值。 |

---
### <a name="mid"></a>Mid
**函数：**<br> Mid(source, start, length)

**说明：**<br> 返回源值的子字符串。 子字符串是一个只包含源字符串中某些字符的字符串。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |通常是属性的名称。 |
| **start** |必选 |integer |**source** 字符串中的索引，子字符串应从此处开始。 字符串中第一个字符的索引为 1，第二个字符的索引为 2，依此类推。 |
| **length** |必选 |integer |子字符串的长度。 如果长度超出 **source** 字符串，则函数将返回从 **start** 索引到 **source** 字符串末尾的子字符串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**函数：**<br> NormalizeDiacritics(source)

**说明：**<br> 需要一个字符串参数。 返回字符串，但将任何标注字符替换为等效的非标注字符。 通常用于将包含标注字符（重音符号）的名字和姓氏转换为可用于各种用户标识符（例如用户主体名称、SAM 帐户名称和电子邮件地址）的合法值。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |需要 |String | 通常是名字或姓氏属性。 |

---
### <a name="not"></a>Not
**函数：**<br> Not(source)

**说明：**<br> 翻转 **source** 的布尔值。 如果 **source** 值为“*True*”，则返回“*False*”。 否则，返回“*True*”。

**参数：**<br> 

| 名称 | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |布尔型字符串 |预期的 **source** 值为“True”或“False”。 |

---
### <a name="replace"></a>将
**函数：**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**说明：**<br>
替换字符串中的值。 其工作方式取决于提供的参数：

* 当提供 **oldValue** 和 **replacementValue** 时：
  
  * 将源中出现的所有 oldValue 替换为 replacementValue
* 当提供 **oldValue** 和 **template** 时：
  
  * 将 **template** 中出现的所有 **oldValue** 都替换为 **source** 值
* 当提供 **regexPattern**、**regexGroupName**、**replacementValue** 时：
  
  * 将源字符串中与 oldValueRegexPattern 匹配的所有值都替换为 replacementValue
* 当提供 **regexPattern**、**regexGroupName**、**replacementPropertyName** 时：
  
  * 如果 **source** 没有值，则返回 **source**
  * 如果 **source** 有值，则使用 **regexPattern** 和 **regexGroupName** 从具有 **replacementPropertyName** 的属性中提取替换值。 替换值作为结果返回

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |通常是来自源对象的属性的名称。 |
| **oldValue** |可选 |String |要在 **source** 或 **template** 中替换的值。 |
| **regexPattern** |可选 |String |要在 **source** 中替换的值的正则表达式模式。 或者，当使用 replacementPropertyName 时，从替换属性中提取值的模式。 |
| **regexGroupName** |可选 |String |**regexPattern** 中的组名称。 仅当使用 replacementPropertyName 时，才会从替换属性中提取此组的值作为 replacementValue。 |
| **replacementValue** |可选 |String |用于替换旧值的新值。 |
| **replacementAttributeName** |可选 |String |当源没有值时，用于替换值的属性名称。 |
| **template** |可选 |String |当提供 **template** 值时，会在模板中查找 **oldValue** 并将其替换为源值。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**函数：**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**说明：**<br> 需要至少两个参数，这些参数是使用表达式定义的唯一值生成规则。 此函数会评估每个规则，然后在目标应用/目录中检查生成的值的唯一性。 将返回找到的第一个唯一值。 如果所有值都已存在于目标中，则会托管该条目并在审核日志中记录原因。 可以提供的参数数目没有上限。

> [!NOTE]
>1. 这是一个顶级函数，不能嵌套。
>2. 此函数仅供用于创建条目。 将其与属性一起使用时，请将“应用映射”  属性设置为“仅在创建对象期间”。 


**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |需要至少 2 个，没有上限 |String | 要评估的唯一值生成规则的列表。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**函数：**<br> SingleAppRoleAssignment([appRoleAssignments])

**说明：**<br> 对于给定的应用程序，从向一个用户分配的所有 appRoleAssignments 列表中返回单个 appRoleAssignment。 需要此函数才能将 appRoleAssignments 对象转换为单个角色名称字符串。 请注意，最佳做法是确保每次只向一个用户分配一个 appRoleAssignment，如果分配了多个角色，则返回的角色字符串可能是不可预测的。 

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必选 |String |**[appRoleAssignments]** 对象。 |

---
### <a name="split"></a>拆分
**函数：**<br> Split(source, delimiter)

**说明：**<br> 使用指定的分隔符字符，将字符串拆分为多值数组。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |要更新的 **source** 值。 |
| **delimiter** |必选 |String |指定将用来拆分字符串的字符（示例：“,”） |

---
### <a name="stripspaces"></a>StripSpaces
**函数：**<br> StripSpaces(source)

**说明：**<br> 从源字符串中删除所有空格 (" ") 字符。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |需要 |String |要更新的 **source** 值。 |

---
### <a name="switch"></a>Switch
**函数：**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**说明：**<br> 当 **source** 值匹配 **key** 时，会返回用于该 **key** 的 **value**。 如果 **source** 值不匹配任何键，则返回 **defaultValue**。  **Key** 和 **value** 参数必须始终成对出现。 该函数始终需要偶数个参数。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |要更新的 **source** 值。 |
| **defaultValue** |可选 |String |当 source 不匹配任何 key 时使用的默认值。 可以是空字符串 ("")。 |
| **key** |必选 |String |用来比较 **source** 值的 **key**。 |
| **value** |必选 |String |与该 key 匹配的 **source** 的替换值。 |

---
### <a name="tolower"></a>ToLower
**函数：**<br> ToLowe（源、区域性）

**说明：**<br> 采用源  字符串值并用指定的区域性规则将其转换为小写。 如果没有指定任何区域性  信息，则使用固定区域性。

**参数：**<br> 

| 名称 | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |通常是来自源对象的属性的名称 |
| **区域性** |可选 |String |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2  ，其中 languagecode2  是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码  。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

---
### <a name="toupper"></a>ToUpper
**函数：**<br> ToUpper（源、区域性）

**说明：**<br> 采用源  字符串值并用指定的区域性规则将其转换为大写。 如果没有指定任何区域性  信息，则使用固定区域性。

**参数：**<br> 

| Name | 必选/重复 | Type | 说明 |
| --- | --- | --- | --- |
| **source** |必选 |String |通常是来自源对象的属性的名称。 |
| **culture** |可选 |String |基于 RFC 4646 的区域性名称格式是 languagecode2-country/regioncode2  ，其中 languagecode2  是两个字母的语言代码，country/regioncode2 是两个字母的子区域性代码  。 示例包括代表日语（日本）的 ja-JP 和代表英语（美国）的 en-US。 在双字母语言代码不可用的情况下，使用派生自 ISO 639-2 的三字母代码。|

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
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**示例输入/输出：**

* **输入** (PreferredFirstName)："John"
* **输入** (PreferredLastName)："Smith"
* **输出**："John.Smith@contoso.com"，如果 UPN 值 John.Smith@contoso.com 尚未存在于目录中
* **输出**："J.Smith@contoso.com"，如果 UPN 值 John.Smith@contoso.com 已存在于目录中
* **输出**："Jo.Smith@contoso.com"，如果上面的两个值已存在于目录中

## <a name="related-articles"></a>相关文章
* [在 SaaS 应用中自动预配和取消预配用户](user-provisioning.md)
* [为用户预配自定义属性映射](customize-application-attributes.md)
* [用于用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](use-scim-to-provision-users-and-groups.md)
* [帐户预配通知](user-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
