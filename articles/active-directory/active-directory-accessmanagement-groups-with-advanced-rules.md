
<properties
	pageTitle="使用属性创建高级规则 | Microsoft Azure"
	description="如何为组创建高级规则，包括支持的表达式规则运算符和参数。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.date="02/09/2016"
	wacn.date=""/>


# 使用属性创建高级规则
Azure 门户允许你灵活地在 Azure Active Directory (Azure AD) 中设置高级规则来启用更复杂的动态组成员身份。

**创建高级规则**：
在 Azure 门户的该组的“配置”选项卡下，选中“高级规则”选项，然后在提供的文本框中键入你的高级规则。你可以使用以下信息创建高级规则。

## 构造高级规则的正文
你可以为动态组成员身份创建的高级规则，本质上是一种由三部分组成并生成 true 或 false 结果的二进制表达式。这三部分如下：

- 左侧参数
- 二进制运算符
- 右侧常量

完整的高级规则如下所示：(leftParameter binaryOperator "RightConstant")，其中，左括号和右括号是整个二进制表达式所必需的，双引号是右侧常量所必需的，左侧参数的语法为 user.property。一个高级规则可能包含由 -and、-or 和 -not 逻辑运算符分隔的多个二进制表达式。下面是正确构造的高级规则的示例：

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

有关支持的参数和表达式规则运算符的完整列表，请参阅以下部分。

高级规则正文的总长度不能超过 2048 个字符。
> [AZURE.NOTE]
>字符串和正则表达式运算不区分大小写。你还可以执行 Null 检查，使用 $null 作为常量，例如 user.department -eq $null。
应该使用 ` 字符来转义包含引号 " 的字符串，例如，user.department -eq "Sa`"les"。

##支持的表达式规则运算符
下表列出所有要在高级规则正文中使用的支持表达式规则运算符及其语法：

| 运算符 | 语法 |
|-----------------|----------------|
| 不等于 | -ne |
| 等于 | -eq |
| 开头不为 | -notStartsWith |
| 开头为 | -startsWith |
| 不包含 | -notContains |
| Contains | -contains |
| 不匹配 | -notMatch |
| 匹配 | -match |


| 查询分析错误 | 错误用法 | 更正的用法 |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 错误: 不支持的属性。 | (user.invalidProperty -eq "Value") | (user.department -eq "value")属性应与上面所列的受支持属性之一匹配。 |
| 错误: 不支持对属性使用运算符。 | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)属性类型为布尔值。对布尔值类型使用上面列出的受支持运算符（-eq 或 -ne）。 |
| 错误: 查询编译错误。 | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")逻辑运算符应与上面所列的受支持属性之一匹配。(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")正则表达式包含错误。 |
| 错误: 二进制表达式的格式不正确。 | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")查询包含多个错误。括号的位置不正确。 |
| 错误: 在设置动态成员身份期间发生未知的错误。 | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")查询包含多个错误。括号的位置不正确。 |

##支持的参数
下面是可以在高级规则中使用的所有用户属性：

**布尔值类型的属性**

允许的操作

* -eq


* -ne


| 属性 | 允许的值 | 使用情况 |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**字符串类型的属性**

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
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | 任意字符串值或 $null。 | (user.city -eq "value") |
| country | 任意字符串值或 $null。 | (user.country -eq "value") |
| department | 任意字符串值或 $null。 | (user.department -eq "value") |
| displayName | 任意字符串值。 | (user.displayName -eq "value") |
| facsimileTelephoneNumber | 任意字符串值或 $null。 | (user.facsimileTelephoneNumber -eq "value") |
| givenName | 任意字符串值或 $null。 | (user.givenName -eq "value") |
| jobTitle | 任意字符串值或 $null。 | (user.jobTitle -eq "value") |
| mail | 任意字符串值或 $null。用户的 SMTP 地址。 | (user.mail -eq "value") |
| mailNickName | 任意字符串值。用户的邮件别名。 | (user.mailNickName -eq "value") |
| mobile | 任意字符串值或 $null。 | (user.mobile -eq "value") |
| objectId | 用户对象的 GUID。 | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | 任意字符串值或 $null。 | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | 任意字符串值或 $null。 | (user.postalCode -eq "value") |
| preferredLanguage | ISO 639-1 代码 | (user.preferredLanguage -eq "en-US") |
| sipProxyAddress | 任意字符串值或 $null。 | (user.sipProxyAddress -eq "value") |
| state | 任意字符串值或 $null。 | (user.state -eq "value") |
| streetAddress | 任意字符串值或 $null。 | (user.streetAddress -eq "value") |
| surname | 任意字符串值或 $null。 | (user.surname -eq "value") |
| telephoneNumber | 任意字符串值或 $null。 | (user.telephoneNumber -eq "value") |
| usageLocation | 双字母国家/地区代码 | (user.usageLocation -eq "US") |
| userPrincipalName | 任意字符串值。 | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

**字符串集合类型的属性**

允许的操作

* -contains


* -notContains

| 属性 | 允许的值 | 使用情况 |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | 任意字符串值 | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## 扩展属性和自定义属性
动态成员身份规则支持扩展属性和自定义属性。

扩展属性从本地 Window Server AD 同步，并采用“ExtensionAttributeX”格式，其中 X 等于 1 - 15。
下面是使用扩展属性的规则示例：

(user.extensionAttribute15 -eq "Marketing")

自定义属性从本地 Windows Server AD 或从连接的 SaaS 应用程序同步，采用“user.extension\_[GUID]\_\_[Attribute]”格式，其中，[GUID] 是在 AAD 中创建该属性的应用程序在 AAD 中的唯一标识符，[Attribute] 是创建的属性的名称。
下面是使用自定义属性的规则示例：

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

可以通过使用图形资源管理器查询用户的属性，以及通过搜索属性名称来查找自定义属性名称。

## 直接下属规则
现在，你可以根据用户的 manager 属性在组中填充成员。
将某个组配置为“经理”组
--------------------------------------------------------------------------------
1. 在管理员门户中，单击“配置”选项卡，然后选择“高级规则”。 
2. 使用以下语法键入规则：Direct Reports for *Direct Reports for {UserID\_of\_manager}*。下面是有效的直接下属规则示例：

Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

其中，"62e19b97-8b3d-4d4a-a106-4ce66896a863" 是管理员的 objectID。可以在 AAD 管理门户中管理员用户的用户页上的配置文件选项卡上找到该对象 ID。

3. 保存此规则时，满足该规则的所有用户将会加入为该组的成员。请注意，最初填充该组可能需要几分钟时间。


## 其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

* [组的动态成员身份疑难解答](/documentation/articles/active-directory-accessmanagement-troubleshooting)

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)

* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)

* [什么是 Azure Active Directory？](active-directory-whatis.md)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)

<!---HONumber=Mooncake_0321_2016-->