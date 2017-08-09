---
title: "基于 Azure Active Directory 中的对象属性动态填充组 | Microsoft Docs"
description: "如何为组成员身份创建高级规则，包括支持的表达式规则运算符和参数。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: curtand
ms.reviewer: rodejo
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: b9b5ddf42958a2b4e241d0252101d979009e7dc0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="populate-groups-dynamically-based-on-object-attributes"></a>基于对象属性动态填充组
可以在 Azure 经典门户中启用 Azure Active Directory (Azure AD) 组的更复杂的基于属性的动态成员身份。  

当用户或设备的任何属性发生更改时，系统会评估目录中的所有动态组规则，以查看该更改是否会触发任何组添加或删除。 如果用户或设备满足组的规则，它们将添加为该组的成员。 如果用户或设备不再满足该规则，则会将其删除。

> [!NOTE]
> - 你可以为安全组或 Office 365 组中的动态成员身份设置规则。
>
> - 此功能需要将每个用户成员的 Azure AD Premium P1 许可证至少添加到一个动态组。
>
> - 可以创建设备或用户的动态组，但不能创建同时包含用户和设备对象的规则。

> - 目前不能基于所有者用户的属性创建设备组。 设备成员身份规则只能引用目录中设备对象的直接属性。

## <a name="to-create-an-advanced-rule"></a>创建高级规则
1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后打开你的组织的目录。
2. 选择“组”选项卡，然后打开要编辑的组。
3. 选择“配置”选项卡，然后选择“高级规则”选项，再在文本框中输入高级规则。

## <a name="constructing-the-body-of-an-advanced-rule"></a>构造高级规则的正文
你可以为动态组成员身份创建的高级规则，本质上是一种由三部分组成并生成 true 或 false 结果的二进制表达式。 这三部分如下：

* 左侧参数
* 二进制运算符
* 右侧常量

完整的高级规则如下所示：(leftParameter binaryOperator "RightConstant")，其中，左括号和右括号是整个二进制表达式所必需的，双引号是右侧常量所必需的，左侧参数的语法为 user.property。 一个高级规则可能包含由 -and、-or 和 -not 逻辑运算符分隔的多个二进制表达式。
下面是正确构造的高级规则的示例：

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

有关支持的参数和表达式规则运算符的完整列表，请参阅以下部分。


请注意，属性必须使用正确的对象类型作为前缀：用户或设备。
以下规则通不过验证：mail –ne null

正确的规则是：

user.mail –ne null

高级规则正文的总长度不能超过 2048 个字符。

> [!NOTE]
> 字符串和正则表达式运算不区分大小写。
> 应该使用 ` 字符来转义包含引号 " 的字符串，例如，user.department -eq \`"Sales"。
> 只能使用引号括住字符串类型值，并且只能使用英文引号。
>
>

## <a name="supported-expression-rule-operators"></a>支持的表达式规则运算符
下表列出所有要在高级规则正文中使用的支持表达式规则运算符及其语法：

| 运算符 | 语法 |
| --- | --- |
| 不等于 |-ne |
| 等于 |-eq |
| 开头不为 |-notStartsWith |
| 开头为 |-startsWith |
| 不包含 |-notContains |
| Contains |-contains |
| 不匹配 |-notMatch |
| 匹配 |-match |
| In | -in |
| 不位于 | -notIn |

## <a name="operator-precedence"></a>运算符优先顺序

下面按从低到高的优先顺序列出了所有运算符，同一行中的运算符具有相同的优先顺序 -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match -notMatch -in -notIn

所有运算符可以带或不带连字符。

请注意，始终都不需要添加括号；仅当优先顺序不符合要求时，才添加括号。例如：

   user.department –eq "Marketing" –and user.country –eq "US"

等效于：

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>使用 -In 和 -notIn 运算符

若要将用户属性的值与大量其他值进行比较，可使用 -In 或 -notIn 运算符。 下面是使用 -In 运算符的示例：

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

请注意值列表开头和末尾使用的“[”和“]”。 此条件的计算结果为 True，user.department 的值与此列表中的某个值相同。

## <a name="query-error-remediation"></a>查询错误更正
下表列出了可能的错误以及当发生这些错误时更正的方法

| 查询分析错误 | 错误用法 | 更正的用法 |
| --- | --- | --- |
| 错误: 不支持的属性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>属性应匹配[支持的属性列表](#supported-properties)中的一个属性。 |
| 错误: 不支持对属性使用运算符。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/>属性为布尔值类型。 对布尔值类型使用上面列出的受支持运算符（-eq 或 -ne）。 |
| 错误: 查询编译错误。 |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>逻辑运算符应匹配上述受支持属性列表中的一个属性。(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")正则表达式中有错误。 |
| 错误: 二进制表达式的格式不正确。 |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>查询包含多个错误。 括号的位置不正确。 |
| 错误: 在设置动态成员身份期间发生未知的错误。 |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>查询包含多个错误。 括号的位置不正确。 |

## <a name="supported-properties"></a>支持的属性
下面是可以在高级规则中使用的所有用户属性：

### <a name="properties-of-type-boolean"></a>布尔值类型的属性
允许的操作

* -eq
* -ne

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>字符串类型的属性
允许的操作

* -eq
* -ne
* -notStartsWith
* -StartsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| city |任意字符串值或 $null |(user.city -eq "value") |
| country |任意字符串值或 $null |(user.country -eq "value") |
| companyName | 任意字符串值或 $null | (user.companyName -eq "value") |
| department |任意字符串值或 $null |(user.department -eq "value") |
| displayName |任意字符串值 |(user.displayName -eq "value") |
| facsimileTelephoneNumber |任意字符串值或 $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任意字符串值或 $null |(user.givenName -eq "value") |
| jobTitle |任意字符串值或 $null |(user.jobTitle -eq "value") |
| mail |任意字符串值或 $null（用户的 SMTP 地址） |(user.mail -eq "value") |
| mailNickName |任意字符串值（用户的邮件别名） |(user.mailNickName -eq "value") |
| mobile |任意字符串值或 $null |(user.mobile -eq "value") |
| objectId |用户对象的 GUID。 |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 从本地同步至云端的用户的本地安全标识符 (SID)。 |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任意字符串值或 $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任意字符串值或 $null |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 代码 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任意字符串值或 $null |(user.sipProxyAddress -eq "value") |
| state |任意字符串值或 $null |(user.state -eq "value") |
| streetAddress |任意字符串值或 $null |(user.streetAddress -eq "value") |
| surname |任意字符串值或 $null |(user.surname -eq "value") |
| telephoneNumber |任意字符串值或 $null |(user.telephoneNumber -eq "value") |
| usageLocation |双字母国家/地区代码 |(user.usageLocation -eq "US") |
| userPrincipalName |任意字符串值 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>字符串集合类型的属性
允许的操作

* -contains
* -notContains

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| otherMails |任意字符串值 |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>多值属性
允许的操作

* -any（当集合中至少有一项符合条件时成立）
* -all（当集合中的所有项都符合条件时成立）

| 属性 | 值 | 使用情况 |
| --- | --- | --- |
| assignedPlans |集合中的每个对象均公开以下字符串属性：capabilityStatus、service、servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

多值属性是同一类型的对象的集合。 可以使用 -any 和 -all 运算符将条件分别应用到集合中的一项或所有项。 例如：

assignedPlans 是多值属性，该项列出了分配给用户的所有服务计划。 以下表达式将选择加入处于启用状态的 Exchange Online (Plan 2) 服务计划的用户：

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

（Guid 标识符标识 Exchange Online (Plan 2) 服务计划。）

> [!NOTE]
> 如果想要找到已启用 Office 365（或其他 Microsoft 联机服务）功能的所有用户（例如，通过一组特定策略定位他们），则此表达式很有用。

以下表达式将选择加入任何与 Intune 服务（由服务名称“SCO”标识）关联的服务计划的所有用户：
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Null 值的用法

若要在规则中指定 null 值，可以使用“null”或 $null。 示例：

   user.mail –ne null 等效于 user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>扩展属性和自定义属性
动态成员身份规则支持扩展属性和自定义属性。

扩展属性从本地 Window Server AD 同步，并采用“ExtensionAttributeX”格式，其中 X 等于 1 - 15。
下面是使用扩展属性的规则示例：

(user.extensionAttribute15 -eq "Marketing")

自定义属性从本地 Windows Server AD 或从连接的 SaaS 应用程序同步，采用“user.extension_[GUID]\__[Attribute]”格式，其中，[GUID] 是在 AAD 中创建该属性的应用程序在 AAD 中的唯一标识符，[Attribute] 是创建的属性的名称。
下面是使用自定义属性的规则示例：

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

可以通过使用图形资源管理器查询用户的属性，以及通过搜索属性名称来查找自定义属性名称。

## <a name="direct-reports-rule"></a>“直接下属”规则
可以创建包含经理的所有直接下属的组。 当经理的直接下属将来发生更改时，组的成员身份将自动进行调整。

> [!NOTE]
> 1. 要使规则起作用，请确保租户中用户的 **Manager ID** 属性已正确设置。 可以在用户的“配置文件”选项卡上查看该用户的当前值。
> 2. 此规则仅支持**直接**下属。 目前不能为嵌套层次结构创建组，例如包括直接下属及其下属的组。

**配置组**

1. 根据[创建高级规则](#to-create-the-advanced-rule)部分中的步骤 1-5 操作，并在“成员身份类型”中选择“动态用户”。
2. 在“动态成员身份规则”边栏选项卡上，使用以下语法输入规则：

    *Direct Reports for "{obectID_of_manager}"*

    有效规则的示例：
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. 保存此规则后，具有指定 Manager ID 值的所有用户都将添加到该组。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用属性创建设备对象的规则
还可以创建一个规则来为组中的成员身份选择设备对象。 可以使用以下设备属性：

| 属性              | 允许的值                  | 使用情况                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| accountEnabled          | true false                      | (device.accountEnabled -eq true)                            |
| displayName             | 任意字符串值                | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | 任意字符串值                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | 任意字符串值                | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | 任意字符串值                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | 任意字符串值                | (device.deviceManufacturer -eq "Microsoft")                 |
| deviceModel             | 任意字符串值                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | 任意字符串值                | (device.deviceOwnership -eq "")                             |
| domainName              | 任意字符串值                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | 任意字符串值                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false                      | (device.deviceOSType -eq true)                              |
| managementType          | 任意字符串值                | (device.managementType -eq "")                              |
| organizationalUnit      | 任意字符串值                | (device.organizationalUnit -eq "")                          |
| deviceId                | 有效 deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | 有效的 AAD objectId            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> 无法在 Azure 经典门户中使用“简单规则”下拉列表创建这些设备规则。
>
>

## <a name="next-steps"></a>后续步骤
这些文章提供了有关 Azure Active Directory 的更多信息。

* [组的动态成员身份疑难解答](active-directory-accessmanagement-troubleshooting.md)
* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)

