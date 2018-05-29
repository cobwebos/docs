---
title: Azure Active Directory 中基于属性的动态组成员身份 | Microsoft Docs
description: 如何为动态组成员身份创建高级规则，包括支持的表达式规则运算符和参数。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/17/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 4eda67f9c28a52667a34af175086be19b627f2ce
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303362"
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>在 Azure Active Directory 中为动态组成员身份创建基于属性的规则
在 Azure Active Directory (Azure AD) 中，可以创建高级规则以启用基于属性的复杂动态组成员身份。 本文详细介绍了用于为用户或设备创建动态成员身份规则的属性和语法。 可以为安全组或 Office 365 组中的动态成员身份设置规则。

当用户或设备的任何属性发生更改时，系统会评估目录中的所有动态组规则，以查看该更改是否会触发任何组添加或删除。 如果用户或设备满足组的规则，它们将添加为该组的成员。 如果用户或设备不再满足该规则，则会将其删除。

> [!NOTE]
> 此功能需要将每个用户成员的 Azure AD Premium P1 许可证至少添加到一个动态组。 并不一定要实际将许可证分配给用户使其成为动态组的成员，但确实需要在租户中具有涵盖所有此类用户所需的最小许可证数。 例如：如果你在租户的所有动态组中总共拥有 1,000 个唯一用户，则需要至少具有 Azure AD Premium P1 或更高版本的 1,000 个许可证，才能满足许可证要求。
>
> 可以创建设备或用户的动态组，但不能创建同时包含用户和设备对象的规则。
> 
> 目前不能基于所有者用户的属性创建设备组。 设备成员身份规则只能引用目录中设备对象的直接属性。

## <a name="to-create-an-advanced-rule"></a>创建高级规则
1. 使用全局管理员或用户帐户管理员的帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“用户和组”。
3. 选择“所有组”，然后选择“新组”。

   ![添加新组](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. 在“组”边栏选项卡上，输入新组的名称和说明。 根据是要为用户还是设备创建规则，在“成员身份类型”中选择“动态用户”或“动态设备”，并选择“添加动态查询”。 可以使用规则生成器生成一个简单的规则，或自己编写一个高级规则。 本文包含有关可用的用户和设备属性，以及高级规则示例的详细信息。

   ![添加动态成员身份规则](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. 创建规则之后，选择边栏选项卡底部的“添加查询”。
6. 在“组”边栏选项卡中，选择“创建”以创建该组。

> [!TIP]
> 如果你输入的高级规则不正确，组创建则可能会失败。 通知将显示在门户的右上角；它包含系统无法接受规则的原因说明。 请仔细阅读，了解如何调整规则以使其生效。

## <a name="constructing-the-body-of-an-advanced-rule"></a>构造高级规则的正文
可以为动态组成员身份创建的高级规则，本质上是一种由三部分组成并生成 true 或 false 结果的二进制表达式。 这三部分如下：

* 左侧参数
* 二进制运算符
* 右侧常量

完整的高级规则如下所示：(leftParameter binaryOperator "RightConstant")，其中，左括号和右括号是整个二进制表达式的可选项；双引号也是可选项，只对于右侧常量是必需的；当它是字符串时，左侧参数的语法为 user.property。 一个高级规则可能包含由 -and、-or 和 -not 逻辑运算符分隔的多个二进制表达式。

下面是正确构造的高级规则的示例：
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
有关支持的参数和表达式规则运算符的完整列表，请参阅以下部分。 有关用于设备规则的属性，请参阅[使用属性创建设备对象的规则](#using-attributes-to-create-rules-for-device-objects)。

高级规则正文的总长度不能超过 2048 个字符。

> [!NOTE]
> 字符串和正则表达式运算不区分大小写。 还可执行 Null 检查，使用 null 作为常量，例如 user.department -eq null。
> 应该使用 ` 字符来转义包含引号 " 的字符串，例如，user.department -eq \`"Sales"。

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

所有运算符在下面按优先级从低到高列出。 同一行上的运算符都采用相同的优先级：
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
所有运算符可以带或不带连字符前缀。 仅当优先级不满足你的要求时，才需要括号。
例如：
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
等效于：
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>使用 -In 和 -notIn 运算符

若要将用户属性的值与大量其他值进行比较，可使用 -In 或 -notIn 运算符。 下面是使用 -In 运算符的示例：
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
请注意值列表开头和末尾使用的“[”和“]”。 此条件的计算结果为 True，user.department 的值与此列表中的某个值相同。


## <a name="query-error-remediation"></a>查询错误更正
下表列出了常见错误以及更正方法

| 查询分析错误 | 错误用法 | 更正的用法 |
| --- | --- | --- |
| 错误: 不支持的属性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>请确保该属性在[支持的属性列表](#supported-properties)中。 |
| 错误: 不支持对属性使用运算符。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>属性类型不支持所使用的运算符（在此示例中，-contains 不能用于布尔类型）。 请对该属性类型使用正确的运算符。 |
| 错误: 查询编译错误。 |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1.缺少运算符。 使用 -and 或 -or 这两个联接谓词<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2. 与 -match 一起使用的正则表达式出错<br/><br/>(user.userPrincipalName -match ".*@domain.ext")，或者：(user.userPrincipalName -match "@domain.ext$")|

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
| city |任意字符串值或 null |(user.city -eq "value") |
| country |任意字符串值或 null |(user.country -eq "value") |
| companyName | 任意字符串值或 null | (user.companyName -eq "value") |
| department |任意字符串值或 null |(user.department -eq "value") |
| displayName |任意字符串值 |(user.displayName -eq "value") |
| employeeId |任意字符串值 |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |任意字符串值或 null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任意字符串值或 null |(user.givenName -eq "value") |
| jobTitle |任意字符串值或 null |(user.jobTitle -eq "value") |
| mail |任意字符串值或 null（用户的 SMTP 地址） |(user.mail -eq "value") |
| mailNickName |任意字符串值（用户的邮件别名） |(user.mailNickName -eq "value") |
| mobile |任意字符串值或 null |(user.mobile -eq "value") |
| objectId |用户对象的 GUID。 |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 从本地同步至云端的用户的本地安全标识符 (SID)。 |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任意字符串值或 null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任意字符串值或 null |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 代码 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任意字符串值或 null |(user.sipProxyAddress -eq "value") |
| state |任意字符串值或 null |(user.state -eq "value") |
| streetAddress |任意字符串值或 null |(user.streetAddress -eq "value") |
| surname |任意字符串值或 null |(user.surname -eq "value") |
| telephoneNumber |任意字符串值或 null |(user.telephoneNumber -eq "value") |
| usageLocation |双字母国家/地区代码 |(user.usageLocation -eq "US") |
| userPrincipalName |任意字符串值 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest null |(user.userType -eq "Member") |

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

* -any（当集合中至少有一项符合条件时满足条件）
* -all（当集合中的所有项都符合条件时满足条件）

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

要在规则中指定 null 值，可以使用 null 值。 注意，不要在 null 这个词两边使用引号 - 如果这样做，它将被解释为文本字符串值。 不能将 -not 运算符用作 null 的比较运算符。 如果使用该运算符，将会出现错误，不管使用 null 还是 $null。 请改用 -eq 或 -ne。 引用 null 值的正确方法如下：
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>扩展属性和自定义属性
动态成员身份规则支持扩展属性和自定义属性。

扩展属性从本地 Window Server AD 同步，并采用“ExtensionAttributeX”格式，其中 X 等于 1 - 15。
下面是使用扩展属性的规则示例：
```
(user.extensionAttribute15 -eq "Marketing")
```
自定义属性从本地 Windows Server AD 或从连接的 SaaS 应用程序同步，采用“user.extension_[GUID]\__[Attribute]”格式，其中，[GUID] 是在 AAD 中创建该属性的应用程序在 AAD 中的唯一标识符，[Attribute] 是创建的属性的名称。
下面是使用自定义属性的规则示例：
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
可以通过使用图形资源管理器查询用户的属性，以及通过搜索属性名称来查找自定义属性名称。

## <a name="direct-reports-rule"></a>“直接下属”规则
可以创建包含经理的所有直接下属的组。 当经理的直接下属将来发生更改时，组的成员身份将自动进行调整。

> [!NOTE]
> 1. 要使规则起作用，请确保租户中用户的 **Manager ID** 属性已正确设置。 可以在用户的“配置文件”选项卡上查看该用户的当前值。
> 2. 此规则仅支持**直接**下属。 目前不能为嵌套层次结构创建组，例如包括直接下属及其下属的组。
> 3. 此规则不能与任何其他的高级规则组合起来使用。

**配置组**

1. 根据[创建高级规则](#to-create-the-advanced-rule)部分中的步骤 1-5 操作，并在“成员身份类型”中选择“动态用户”。
2. 在“动态成员身份规则”边栏选项卡上，使用以下语法输入规则：

    *Direct Reports for "{objectID_of_manager}"*

    有效规则的示例：
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. 保存此规则后，具有指定 Manager ID 值的所有用户都将添加到该组。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用属性创建设备对象的规则
还可以创建一个规则来为组中的成员身份选择设备对象。 可以使用以下设备属性。

 设备属性  | 值 | 示例
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | 任意字符串值 |(device.displayName -eq "Rob Iphone”)
 deviceOSType | 任意字符串值 | (device.deviceOSType -eq "iPad") -或 (device.deviceOSType -eq "iPhone")
 deviceOSVersion | 任意字符串值 | (device.OSVersion -eq "9.1")
 deviceCategory | 有效的设备类别名称 | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | 任意字符串值 | (device.deviceManufacturer -eq "Samsung")
 deviceModel | 任意字符串值 | (device.deviceModel -eq "iPad Air")
 deviceOwnership | 个人、公司、未知 | (device.deviceOwnership -eq "Company")
 domainName | 任意字符串值 | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Apple 设备注册配置文件或 Windows Autopilot 配置文件名称 | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM（适用于移动设备）<br>电脑（适用于由 Intune 电脑代理管理的计算机） | (device.managementType -eq "MDM")
 organizationalUnit | 由本地 Active Directory 设置的与组织单位名称匹配的任何字符串值 | (device.organizationalUnit -eq "US PCs")
 deviceId | 有效的 Azure AD 设备 ID | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | 有效的 Azure AD 对象 ID |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>将动态成员身份更改为静态，或反之
可更改在组中管理成员身份的方式。 想要在系统中保留相同的组名称和 ID，使针对组的任何现有引用仍然有效时，这很有用；创建新组需要更新这些引用。

我们已更新 Azure AD 管理中心，以添加对此功能的支持。 现在，客户可将现有组从动态成员身份转换为分配的成员身份，或反之，不管是通过 Azure AD 管理中心还是 PowerShell cmdlet 来进行，如下所示。

> [!WARNING]
> 将现有静态组更改为动态组时，会从组中删除所有现有成员，然后会处理成员身份规则以添加新成员。 如果使用组来控制对应用或资源的访问，则在完全处理成员身份规则前，原始成员可能无法进行访问。
>
> 建议事先测试新的成员身份规则，确保组中的新成员身份符合预期。

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>使用 Azure AD 管理中心更改组中的成员身份管理 

1. 在租户中使用全局管理员或用户帐户管理员的帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“组”。
3. 在“所有组”列表中，打开要更改的组。
4. 选择“属性”。
5. 在组的“属性”页中，选择“已分配(静态)”、“动态用户”或“动态设备”作为“成员身份类型”，具体取决于所需的成员身份类型。 对于动态成员身份，可以使用规则生成器来选择适用于简单规则的选项，也可以自行编写一项高级规则。 

以下步骤以示例的方式说明了如何将某个组的用户从静态成员身份更改为动态成员身份。 

1. 在所选组的“属性”页中，选择“动态用户”作为“成员身份类型”，然后在说明对组成员身份所做更改的对话框中选择“是”，以便继续操作。 
  
   ![选择“动态用户”作为“成员身份类型”](./media/active-directory-groups-dynamic-membership-azure-portal/select-group-to-convert.png)
  
2. 选择“添加动态查询”，然后提供规则。
  
   ![输入规则](./media/active-directory-groups-dynamic-membership-azure-portal/enter-rule.png)
  
3. 创建规则之后，选择页面底部的“添加查询”。
4. 在组的“属性”页上选择“保存”，以便保存所做的更改。 组列表中组的“成员身份类型”会立即进行更新。

> [!TIP]
> 如果所输入的高级规则不正确，组转换操作可能会失败。 通知显示在门户的右上角；它包含系统无法接受规则的原因说明。 请仔细阅读，了解如何调整规则才能使其生效。

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>使用 PowerShell 更改组中的成员身份管理

> [!NOTE]
> 若要更改动态组属性，需要通过 [Azure AD PowerShell 版本 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)（即**预览版**）使用 cmdlet。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureADPreview)安装预览版。

下面是在现有组中切换成员身份管理的函数示例。 在此示例中，必须小心正确地操作 GroupTypes 属性，并保留与动态成员身份无关的任何值。

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
将组设为静态：
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
将组设为动态：
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>后续步骤
以下文章提供了有关 Azure Active Directory 中的组的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](active-directory-groups-create-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)
