---

title: "Azure Active Directory 预览版中基于属性的动态组成员身份 | Microsoft 文档"
description: "如何为动态组成员身份创建高级规则，包括支持的表达式规则运算符和参数。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 58768cd59a922483bcb37797a6dcd515d159ef4c
ms.openlocfilehash: 16dd02934d4bd18f87f6508dce6c6829fe6aa0ac
ms.lasthandoff: 03/01/2017


---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory-preview"></a>在 Azure Active Directory 预览版中为动态组成员身份创建基于属性的规则
可以在 Azure 门户中创建高级规则来启用 Azure Active Directory (Azure AD) 预览版组的更复杂的基于属性的动态成员身份。 [预览版包括哪些内容？](active-directory-preview-explainer.md) 

本文详细介绍了用于创建动态成员身份规则的属性和语法。

## <a name="to-create-the-advanced-rule"></a>创建高级规则
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入“用户和组”，然后选择 **Enter**。

   ![打开“用户管理”](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)
3. 在“用户和组”边栏选项卡中，选择“所有组”。

   ![打开“组”边栏选项卡](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)
4. 在“用户和组 - 所有组”边栏选项卡中，选择“添加”命令。

   ![添加新组](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)
5. 在“组”边栏选项卡上，输入新组的名称和说明。 根据是要为用户还是设备创建规则，在“成员身份类型”中选择“动态用户”或“动态设备”，然后选择“添加动态查询”。 有关用于设备规则的属性，请参阅[使用属性创建设备对象的规则](#using-attributes-to-create-rules-for-device-objects)。

   ![添加动态成员身份规则](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)
6. 在“动态成员身份规则”边栏选项卡上的“添加动态成员身份高级规则”框中输入规则，然后在边栏选项卡底部选择“创建”。
7. 在“组”边栏选项卡中，选择“创建”以创建该组。

## <a name="constructing-the-body-of-an-advanced-rule"></a>构造高级规则的正文
你可以为动态组成员身份创建的高级规则，本质上是一种由三部分组成并生成 true 或 false 结果的二进制表达式。 这三部分如下：

* 左侧参数
* 二进制运算符
* 右侧常量

完整的高级规则如下所示：(leftParameter binaryOperator "RightConstant")，其中，左括号和右括号是整个二进制表达式所必需的，双引号是右侧常量所必需的，左侧参数的语法为 user.property。 一个高级规则可能包含由 -and、-or 和 -not 逻辑运算符分隔的多个二进制表达式。

下面是正确构造的高级规则的示例：

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

有关支持的参数和表达式规则运算符的完整列表，请参阅以下部分。 有关用于设备规则的属性，请参阅[使用属性创建设备对象的规则](#using-attributes-to-create-rules-for-device-objects)。

高级规则正文的总长度不能超过 2048 个字符。

> [!NOTE]
> 字符串和正则表达式运算不区分大小写。 你还可以执行 Null 检查，使用 $null 作为常量，例如 user.department -eq $null。
> 应该使用 ` 字符来转义包含引号 " 的字符串，例如，user.department -eq \`"Sales"。
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
| accountEnabled |true false |user.accountEnabled -eq true) |
| dirSyncEnabled |true false null |(user.dirSyncEnabled -eq true) |

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

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| city |任意字符串值或 $null |(user.city -eq "value") |
| country |任意字符串值或 $null |(user.country -eq "value") |
| CompanyName | 任意字符串值或 $null | (user.CompanyName-eq"value") |
| department |任意字符串值或 $null |(user.department -eq "value") |
| displayName |任意字符串值 |(user.displayName -eq "value") |
| facsimileTelephoneNumber |任意字符串值或 $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任意字符串值或 $null |(user.givenName -eq "value") |
| jobTitle |任意字符串值或 $null |(user.jobTitle -eq "value") |
| mail |任意字符串值或 $null（用户的 SMTP 地址） |(user.mail -eq "value") |
| mailNickName |任意字符串值（用户的邮件别名） |(user.mailNickName -eq "value") |
| mobile |任意字符串值或 $null |(user.mobile -eq "value") |
| objectId |用户对象的 GUID。 |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
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

## <a name="extension-attributes-and-custom-attributes"></a>扩展属性和自定义属性
动态成员身份规则支持扩展属性和自定义属性。

扩展属性从本地 Window Server AD 同步，并采用“ExtensionAttributeX”格式，其中 X 等于 1 - 15。
下面是使用扩展属性的规则示例：

(user.extensionAttribute15 -eq "Marketing")

自定义属性从本地 Windows Server AD 或从连接的 SaaS 应用程序同步，采用“user.extension_[GUID]\__[Attribute]”格式，其中，[GUID] 是在 AAD 中创建该属性的应用程序在 AAD 中的唯一标识符，[Attribute] 是创建的属性的名称。
下面是使用自定义属性的规则示例：

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

可以通过使用图形资源管理器查询用户的属性，以及通过搜索属性名称来查找自定义属性名称。

## <a name="direct-reports-rule"></a>直接下属规则
现在，你可以根据用户的 manager 属性在组中填充成员。

**将某个组配置为“经理”组**

1. 根据[创建高级规则](#to-create-the-advanced-rule)中的步骤 1-5 操作，然后在“成员身份类型”中选择“动态用户”。
2. 在“动态成员身份规则”边栏选项卡上，使用以下语法输入规则：

    Direct Reports for *Direct Reports for {obectID_of_manager}*。 下面是有效的直接下属规则示例：

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    其中，"62e19b97-8b3d-4d4a-a106-4ce66896a863" 是管理员的 objectID。 可以在 Azure AD 的管理员用户的用户页上的“个人资料”选项卡中找到该对象 ID。
3. 保存此规则时，满足该规则的所有用户将会加入为该组的成员。 最初填充该组可能需要几分钟时间。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用属性创建设备对象的规则
还可以创建一个规则来为组中的成员身份选择设备对象。 可以使用以下设备属性：

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| displayName |任意字符串值 |(device.displayName -eq "Rob Iphone”) |
| deviceOSType |任意字符串值 |(device.deviceOSType -eq "IOS") |
| deviceOSVersion |任意字符串值 |(device.OSVersion -eq "9.1") |
| isDirSynced |true false null |(device.isDirSynced -eq "true") |
| isManaged |true false null |(device.isManaged -eq "false") |
| isCompliant |true false null |(device.isCompliant -eq "true") |

## <a name="next-steps"></a>后续步骤
以下文章提供了有关 Azure Active Directory 中的组的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](active-directory-groups-create-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)

