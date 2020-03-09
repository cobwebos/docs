---
title: 动态组成员身份的规则-Azure AD |Microsoft Docs
description: 如何创建成员身份规则以自动填充组和规则引用。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: da983f87977de922ec547c3ade2972dfb4d69363
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374764"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Azure Active Directory 中组的动态成员身份规则

在 Azure Active Directory （Azure AD）中，可以创建复杂的基于属性的规则，以启用组的动态成员身份。 动态组成员身份可以减少添加和删除用户的管理开销。 本文详细介绍为用户或设备创建动态成员身份规则的属性和语法。 你可以为安全组或 Office 365 组的动态成员身份设置规则。

当用户或设备的任何属性发生更改时，系统将评估目录中的所有动态组规则，以查看更改是否会触发任何组添加或删除。 如果用户或设备满足组中的规则，则会将其添加为该组的成员。 如果不再满足规则，则会将其删除。 不能手动添加或删除动态组的成员。

- 你可以为设备或用户创建动态组，但不能创建同时包含用户和设备的规则。
- 不能基于设备所有者的属性创建设备组。 设备成员身份规则只能引用设备属性。

> [!NOTE]
> 此功能要求是一个或多个动态组成员的每个唯一用户的 Azure AD Premium P1 许可证。 无需为用户分配许可证作为动态组的成员，但必须在租户中拥有最小数量的许可证才能涵盖所有此类用户。 例如，如果你的租户中的所有动态组共有1000个唯一用户，则需要至少1000个 Azure AD Premium P1 许可证才能满足许可证要求。
> 如果设备是动态设备组的成员，则不需要许可证。

## <a name="rule-builder-in-the-azure-portal"></a>Azure 门户中的规则生成器

Azure AD 提供了一个规则生成器，以便更快地创建和更新重要规则。 规则生成器支持的构造最多为五个表达式。 规则生成器使使用几个简单的表达式来形成规则变得更加容易，但是，它不能用于重现每个规则。 如果规则生成器不支持要创建的规则，则可以使用文本框。

下面是我们建议使用文本框构造的高级规则或语法的一些示例：

- 具有五个以上表达式的规则
- 直接下属规则
- 设置[运算符优先级](groups-dynamic-membership.md#operator-precedence)
- [包含复杂表达式的规则](groups-dynamic-membership.md#rules-with-complex-expressions);例如 `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 规则生成器可能无法显示在文本框中构造的某些规则。 当规则生成器无法显示规则时，可能会看到一条消息。 规则生成器不会以任何方式更改动态组规则的支持语法、验证或处理。

有关分步说明，请参阅[创建或更新动态组](groups-create-rule.md)。

![添加动态组的成员身份规则](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>单个表达式的规则语法

单个表达式是成员身份规则最简单的形式，并且只有上述三个部分。 带有单个表达式的规则如下所示： `Property Operator Value`，其中属性的语法是对象. 属性的名称。

下面是使用单个表达式正确构造的成员身份规则的示例：

```
user.department -eq "Sales"
```

对于单个表达式，括号是可选的。 成员身份规则正文的总长度不能超过2048个字符。

## <a name="constructing-the-body-of-a-membership-rule"></a>构造成员身份规则的主体

自动使用用户或设备填充组的成员身份规则是导致 true 或 false 结果的二进制表达式。 简单规则的三个部分是：

- 知识产权
- 操作员
- 负值

表达式中部分的顺序非常重要，目的是避免语法错误。

## <a name="supported-properties"></a>支持的属性

有三种类型的属性可用于构造成员身份规则。

- 变量
- 类似
- 字符串集合

下面是可用于创建单个表达式的用户属性。

### <a name="properties-of-type-boolean"></a>布尔值类型的属性

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| accountEnabled |true false |accountEnabled-eq true |
| dirSyncEnabled |true false |dirSyncEnabled-eq true |

### <a name="properties-of-type-string"></a>字符串类型的属性

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| 梵蒂冈 |任何字符串值或*null* |（用户 city-eq "value"） |
| 该国 |任何字符串值或*null* |（user. country-eq "value"） |
| 公司 | 任何字符串值或*null* | （用户名称-eq "value"） |
| 部门 |任何字符串值或*null* |（"用户"-eq "value"） |
| displayName |任何字符串值 |（user. displayName-eq "value"） |
| employeeId |任何字符串值 |（用户 Id-eq "value"）<br>（用户 Id-ne *null*） |
| facsimileTelephoneNumber |任何字符串值或*null* |（facsimileTelephoneNumber-eq "value"） |
| givenName |任何字符串值或*null* |（givenName-eq "value"） |
| jobTitle |任何字符串值或*null* |（jobTitle-eq "value"） |
| 信 |任何字符串值或*null* （用户的 SMTP 地址） |（user. mail-eq "value"） |
| mailNickName |任何字符串值（用户的邮件别名） |（mailNickName-eq "value"） |
| 便携式 |任何字符串值或*null* |（user. mobile-eq "value"） |
| objectId |用户对象的 GUID |（用户. objectId-eq "11111111-1111-1111-1111-111111111111"） |
| onPremisesSecurityIdentifier | 从本地同步到云的用户的本地安全标识符（SID）。 |（onPremisesSecurityIdentifier-eq "S-1-1-11-1111111111-1111111111-1111111111-1111111"） |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration，DisableStrongPassword |（passwordPolicies-eq "DisableStrongPassword"） |
| physicalDeliveryOfficeName |任何字符串值或*null* |（physicalDeliveryOfficeName-eq "value"） |
| postalCode |任何字符串值或*null* |（user. 邮政编码-eq "value"） |
| User.preferredlanguage |ISO 639-1 代码 |（User.preferredlanguage-eq "en-us"） |
| sipProxyAddress |任何字符串值或*null* |（sipProxyAddress-eq "value"） |
| 状态 |任何字符串值或*null* |（user. state-eq "value"） |
| streetAddress |任何字符串值或*null* |（streetAddress-eq "value"） |
| 姓 |任何字符串值或*null* |（user. 姓-eq "value"） |
| telephoneNumber |任何字符串值或*null* |（telephoneNumber-eq "value"） |
| usageLocation |双字母国家/地区代码 |（usageLocation-eq "US"） |
| userPrincipalName |任何字符串值 |（用户. userPrincipalName-eq "alias@domain"） |
| userType |成员来宾*null* |（userType-eq "Member"） |

### <a name="properties-of-type-string-collection"></a>字符串集合类型的属性

| 属性 | 允许的值 | 使用情况 |
| --- | --- | --- |
| User.othermails |任何字符串值 |（User.othermails-包含 "alias@domain"） |
| proxyAddresses |SMTP： alias@domain smtp： alias@domain |（proxyAddresses-包含 "SMTP： alias@domain"） |

有关用于设备规则的属性，请参阅[设备的规则](#rules-for-devices)。

## <a name="supported-expression-operators"></a>支持的表达式运算符

下表列出了所有支持的运算符及其用于单个表达式的语法。 运算符可以与或不带连字符（-）前缀一起使用。

| 操作员 | 语法 |
| --- | --- |
| 不等于 |-ne |
| 对应 |-eq |
| 开头不为 |-notStartsWith |
| 开头为 |-startsWith |
| 不包含 |-notContains |
| 有 |-contains |
| 不匹配 |-notMatch |
| 适应 |-match |
| 中 | -在中 |
| 不在 | -notIn |

### <a name="using-the--in-and--notin-operators"></a>使用-in 和-notIn 运算符

如果要将用户属性的值与多个不同值进行比较，可以使用-in 或-notIn 运算符。 使用括号符号 "[" 和 "]" 开始和结束值列表。

 在下面的示例中，如果 "用户" 的值等于列表中的任何值，则表达式的计算结果为 true：

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>使用-match 运算符 
**-Match**运算符用于匹配任何正则表达式。 示例

```
user.displayName -match "Da.*"   
```
Da，Dav，David 的计算结果为 true，则 aDa 的计算结果为 false。

```
user.displayName -match ".*vid"
```
David 的计算结果为 true，Da 的计算结果为 false。

## <a name="supported-values"></a>支持的值

表达式中使用的值可以包含若干类型，包括：

* Strings
* 布尔值– true、false
* 小数
* 数组–数字数组，字符串数组

在表达式中指定值时，请务必使用正确的语法来避免错误。 一些语法提示如下：

* 双引号是可选的，除非值为字符串。
* 字符串和正则表达式运算不区分大小写。
* 如果字符串值包含双引号，则这两个引号都应该使用 \` 字符进行转义，例如，在 "Sales" 是值时 \`，"Sales\`" 是正确的语法。
* 你还可以执行 Null 检查，并使用 null 作为值，例如 `user.department -eq null`。

### <a name="use-of-null-values"></a>使用 Null 值

若要在规则中指定 null 值，可以使用*null*值。 

* 比较表达式中的*null*值时，请使用-eq 或-ne。
* 仅当你希望将它解释为文本字符串值时，才在单词*null*两侧使用引号。
* -Not 运算符不能用作 null 的比较运算符。 如果使用它，则会收到一个错误，指示你使用的是 null 还是 $null。

引用 null 值的正确方法如下：

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>具有多个表达式的规则

组成员身份规则可以包含由-and、-or 和-not 逻辑运算符连接的多个单个表达式。 逻辑运算符也可以组合使用。 

下面的示例演示了具有多个表达式的正确构造成员身份规则：

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>运算符优先级

下面按优先级从高到低的顺序列出了所有运算符。 同一行中的运算符具有相同的优先级：

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

下面是一个运算符优先级示例，其中为用户计算了两个表达式：

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

仅当优先顺序不符合要求时，才需要括号。 例如，如果你想要先评估部门，以下内容说明了如何使用括号来确定顺序：

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>包含复杂表达式的规则

成员身份规则可以包含复杂表达式，其中的属性、运算符和值采用更复杂的形式。 当满足以下任一条件时，表达式被视为复杂表达式：

* 属性由值的集合组成;具体而言，多值属性
* 表达式使用-any 和-all 运算符
* 表达式的值本身可以是一个或多个表达式

## <a name="multi-value-properties"></a>多值属性

多值属性是同一类型的对象的集合。 它们可用于使用-any 和-all 逻辑运算符创建成员身份规则。

| 属性 | 分隔 | 使用情况 |
| --- | --- | --- |
| assignedPlans | 集合中的每个对象都公开以下字符串属性： capabilityStatus、service、servicePlanId |assignedPlans-any （assignedPlan. servicePlanId-eq ' efb87545-963c-4e0d-99df-69c6916d9eb0 '-and assignedPlan. capabilityStatus-eq ' Enabled '） |
| proxyAddresses| SMTP： alias@domain smtp： alias@domain | （proxyAddresses-any （\_-contains "contoso"）） |

### <a name="using-the--any-and--all-operators"></a>使用-any 和-all 运算符

可以使用-any 和-all 运算符分别将条件应用于集合中的一个或全部项。

* -any （当集合中至少有一项与条件匹配时满足此条件）
* -all （当集合中的所有项符合条件时满足此条件）

#### <a name="example-1"></a>示例1

assignedPlans 是一个多值属性，它列出分配给用户的所有服务计划。 下面的表达式选择具有也处于启用状态的 Exchange Online （计划2）服务计划（作为 GUID 值）的用户：

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

此类规则可用于对为其启用 Office 365 （或其他 Microsoft Online Service）功能的所有用户进行分组。 然后，可以将一组策略应用于组。

#### <a name="example-2"></a>示例2

以下表达式将选择具有与 Intune 服务关联的任何服务计划的所有用户（由服务名称 "SCO" 标识）：

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>使用下划线（\_）语法

下划线（\_）语法匹配某个多值字符串集合属性中特定值的匹配项，以将用户或设备添加到动态组。 它与-any 或-all 运算符一起使用。

下面是一个示例，说明如何在规则中使用下划线（\_）来添加基于 proxyAddress 的成员。 此规则将包含代理地址的任何用户添加到组中。

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>其他属性和通用规则

### <a name="create-a-direct-reports-rule"></a>创建 "直接下属" 规则

您可以创建包含经理的所有直接下属的组。 当经理的直接下属发生变化时，会自动调整组的成员身份。

直接下属规则使用以下语法构造：

```
Direct Reports for "{objectID_of_manager}"
```

下面是一个有效规则的示例，其中 "62e19b97-8b3d-4d4a-a106-4ce66896a863" 是经理的 objectID：

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

以下提示可帮助你正确使用该规则。

- **管理器 id**是经理的对象 id。 可以在管理器的**配置文件**中找到它。
- 要使规则正常运行，请确保为租户中的用户正确设置了**管理器**属性。 可以在用户的**配置文件**中检查当前值。
- 此规则仅支持经理的直接下属。 换句话说，您不能使用经理的直接下属*和*报表来创建组。
- 此规则不能与任何其他成员身份规则结合。

### <a name="create-an-all-users-rule"></a>创建 "所有用户" 规则

可以使用成员身份规则创建包含租户中所有用户的组。 如果将来在租户中添加或删除用户，则会自动调整组的成员身份。

"所有用户" 规则是使用单表达式构造的，使用-ne 运算符和 null 值。 此规则将 B2B 来宾用户和成员用户添加到组。

```
user.objectId -ne null
```
如果你希望组排除来宾用户并且只包括你的租户的成员，则可以使用以下语法：

```
(user.objectId -ne null) -and (user.userType -eq “Member”)
```

### <a name="create-an-all-devices-rule"></a>创建 "所有设备" 规则

你可以使用成员身份规则创建包含租户中所有设备的组。 将来在租户中添加或删除设备时，会自动调整组的成员身份。

"所有设备" 规则使用单个表达式构造，使用-ne 运算符和 null 值：

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>扩展属性和自定义扩展属性

支持在动态成员身份规则中将扩展属性和自定义扩展属性作为字符串属性。 [扩展属性](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0)从本地 WINDOW Server AD 同步，采用 "ExtensionAttributeX" 格式，其中 X 等于 1-15。 下面是使用扩展属性作为属性的规则示例：

```
(user.extensionAttribute15 -eq "Marketing")
```

[自定义扩展属性](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions)从本地 WINDOWS Server AD 或从连接的 SaaS 应用程序同步，其格式为 `user.extension_[GUID]_[Attribute]`，其中：

* [GUID] 是在 Azure AD 中创建属性的应用程序 Azure AD 中的唯一标识符。
* [Attribute] 是创建的属性的名称

使用自定义扩展属性的规则示例如下：

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

可以通过使用图形资源管理器查询用户的属性并搜索属性名称，在目录中找到自定义属性名称。 此外，你现在可以选择 "动态用户组规则生成器" 中的 "**获取自定义扩展属性**" 链接来输入唯一的应用 ID，并接收创建动态成员身份规则时要使用的自定义扩展属性的完整列表。 也可以刷新此列表，以获取该应用的任何新的自定义扩展属性。

## <a name="rules-for-devices"></a>设备的规则

你还可以创建一个规则来为组中的成员身份选择设备对象。 不能将用户和设备作为组成员。 

> [!NOTE]
> **OrganizationalUnit**属性不再列出，因此不应使用。 此字符串由 Intune 在特定情况下进行设置，但 Azure AD 无法识别，因此不会将任何设备添加到基于此属性的组。

> [!NOTE]
> systemlabels 是不能与 Intune 一起设置的只读属性。
>
> 对于 Windows 10，deviceOSVersion 属性的正确格式如下：（deviceOSVersion-eq "10.0.17763"）。 可以通过 Get-msoldevice PowerShell cmdlet 验证格式设置。

可以使用下列设备属性。

 设备属性  | 分隔 | 实例
 ----- | ----- | ----------------
 accountEnabled | true false | （accountEnabled-eq true）
 displayName | 任何字符串值 |（device. displayName-eq "抢 iPhone"）
 Device.deviceostype | 任何字符串值 | （Device.deviceostype-eq "iPad"）-或（Device.deviceostype-eq "iPhone"）<br>（Device.deviceostype-包含 "AndroidEnterprise"）<br>（Device.deviceostype-eq "AndroidForWork"）
 deviceOSVersion | 任何字符串值 | （deviceOSVersion-eq "9.1"）
 Device.devicecategory | 有效的设备类别名称 | （Device.devicecategory-eq "BYOD"）
 Device.devicemanufacturer | 任何字符串值 | （Device.devicemanufacturer-eq "Samsung"）
 deviceModel | 任何字符串值 | （deviceModel-eq "iPad Air"）
 Device.deviceownership | 个人、公司、未知 | （Device.deviceownership-eq "公司"）
 Device.enrollmentprofilename | Apple 设备注册配置文件、设备注册-企业设备标识符（Android-展台）或 Windows Autopilot 配置文件名称 | （Device.enrollmentprofilename-eq "DEP Iphone"）
 Device.isrooted | true false | （Device.isrooted-eq true）
 Device.managementtype | MDM （适用于移动设备）<br>PC （适用于由 Intune PC 代理管理的计算机） | （Device.managementtype-eq "MDM"）
 deviceId | 有效 Azure AD 设备 ID | （d4fe7726-5966-431c-b3b8-cddc8fdb717d-eq ""）
 objectId | 有效的 Azure AD 对象 ID |  （设备 objectId-eq "76ad43c9-32c5-45e8-a272-7b58b58f596d"）
 devicePhysicalIds | Autopilot 使用的任何字符串值，如所有 Autopilot 设备、订单 Id 或 PurchaseOrderID  | （devicePhysicalIDs-任意 _-包含 "[ZTDId]"）（devicePhysicalIds-任意 _-eq "[订单 Id]： 179887111881"）（devicePhysicalIds-任意 _-eq "[PurchaseOrderId]： 76222342342"）
 systemLabels | 与用于标记新式工作区设备的 Intune 设备属性匹配的任何字符串 | （systemLabels-包含 "M365Managed"）

> [!Note]  
> 对于 Device.deviceownership 为设备创建动态组时，需要将值设置为 "Company"。 在 Intune 上，设备所有权表示为 "公司"。 有关更多详细信息，请参阅[OwnerTypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) 。 

## <a name="next-steps"></a>后续步骤

这些文章提供了有关 Azure Active Directory 中的组的详细信息。

- [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [创建新组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)
- [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-create-rule.md)
