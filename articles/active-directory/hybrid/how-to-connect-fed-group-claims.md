---
title: 为具有 Azure Active Directory 的应用程序配置组声明 |Microsoft Docs
description: 有关如何配置与 Azure AD 一起使用的组声明的信息。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 6a89c5e3fb84f797d9ad7f81626fb7185ce3e076
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854164"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>为应用程序配置组声明 Azure Active Directory

Azure Active Directory 可以提供令牌中的用户组成员身份信息，以供在应用程序中使用。  支持以下两种主要模式：

- 由其 Azure Active Directory 对象标识符（OID）特性标识的组
- 由 sAMAccountName 或 GroupSID 属性为 Active Directory （AD）同步组和用户标识的组

> [!IMPORTANT]
> 此功能需要注意一些注意事项：
>
>- 支持使用从本地同步的 sAMAccountName 和安全标识符（SID）特性，旨在实现从 AD FS 和其他标识提供程序移动现有应用程序。 在 Azure AD 中管理的组不包含发出这些声明所需的属性。
>- 在较大的组织中，用户所属的组的数量可能会超出 Azure Active Directory 将添加到令牌中的限制。 150组适用于 SAML 令牌，200用于 JWT。 这可能导致不可预知的结果。 如果用户的组成员数量很大，我们建议使用选项将在声明中发出的组限制为应用程序的相关组。  
>- 对于新的应用程序开发，或在可以为应用程序配置应用程序的情况下，以及不需要嵌套组支持的情况下，我们建议在应用程序中授权基于应用程序角色而不是组。  这会限制需要传入令牌的信息量，更安全，并可将用户分配与应用配置分隔开来。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>从 AD FS 和其他标识提供程序迁移的应用程序的组声明

许多配置为用 AD FS 进行身份验证的应用程序依赖于 Windows AD 组属性形式的组成员身份信息。   这些属性是组 sAMAccountName，可由域名限定，或 Windows 组安全标识符（GroupSID）。  当应用程序与 AD FS 联合时，AD FS 使用 TokenGroups 函数来检索用户的组成员身份。

已从 AD FS 移动的应用需要采用相同格式的声明。 可以从包含域限定的 sAMAccountName 或从 Active Directory （而不是组的 Azure Active Directory objectID）同步的 Azure Active Directory 发出组和角色声明。

组声明支持的格式为：

- **Azure Active Directory 组 ObjectId** （适用于所有组）
- **sAMAccountName** （可用于从 Active Directory 同步的组）
- **NetbiosDomain\sAMAccountName** （可用于从 Active Directory 同步的组）
- **DNSDomainName\sAMAccountName** （可用于从 Active Directory 同步的组）
- **本地组安全标识符**（可用于从 Active Directory 同步的组）

> [!NOTE]
> sAMAccountName 和本地组 SID 属性仅适用于从 Active Directory 同步的组对象。   它们不可用于在 Azure Active Directory 或 Office365 中创建的组。   在 Azure Active Directory 中配置为同步本地组属性的应用程序仅为同步的组获取这些属性。

## <a name="options-for-applications-to-consume-group-information"></a>应用程序使用组信息的选项

应用程序可以调用 MS Graph 组终结点，以获取经过身份验证的用户的组信息。 此调用可以确保用户所属的所有组在涉及大量组时仍可用。  组枚举随后独立于标记大小限制。

但是，如果现有应用程序需要通过声明使用组信息，则可以使用多种不同的声明格式来配置 Azure Active Directory。  请考虑以下选项：

- 将组成员身份用于应用程序内授权时，最好使用组 ObjectID。 组 ObjectID 在 Azure Active Directory 中是不可变的，并且可用于所有组。
- 如果使用本地组 sAMAccountName 进行授权，请使用域限定名称; 名称冲突的可能性较低。 sAMAccountName 在 Active Directory 域中可能是唯一的，但如果有多个 Active Directory 域与某个 Azure Active Directory 租户同步，则可能存在多个同名的组。
- 请考虑使用[应用程序角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)提供组成员身份和应用程序之间的间接层。   然后，应用程序会根据令牌中的角色 clams 做出内部授权决定。
- 如果将应用程序配置为获取从 Active Directory 同步的组属性，并且组不包含这些属性，则不会将其包含在声明中。
- 标记中的组声明包括嵌套组，但使用选项将组声明限制为分配给应用程序的组时除外。  如果用户是 GroupB 的成员，并且 GroupB 为 GroupA 的成员，则该用户的组声明将同时包含 GroupA 和 GroupB。 当组织的用户具有大量组成员身份时，令牌中列出的组数可能会增长令牌大小。  Azure Active Directory 将在令牌中发出的组数限制为 SAML 断言为150，为 JWT 限制为200。  如果用户是较大组的成员，则会忽略这些组，而是改为包含用于获取组信息的图形端点的链接。

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>使用从 Active Directory 同步的组属性的先决条件

如果使用 ObjectId 格式，则可以在任何组的令牌中发出组成员资格声明。 若要使用组 ObjectId 以外的其他格式的组声明，必须使用 Azure AD Connect 从 Active Directory 同步组。

配置 Azure Active Directory 以发出 Active Directory 组的组名的步骤有两个。

1. **从 Active Directory 同步组名称**在 Azure Active Directory 可以在组或角色声明中发出组名或本地组 SID 之前，需要从 Active Directory 同步所需的属性。  必须运行1.2.70 或更高版本的 Azure AD Connect。   早于1.2.70 的 Azure AD Connect 将从 Active Directory 同步组对象，但不包括所需的组名称属性。  升级到当前版本。

2. **将 Azure Active Directory 中的应用程序注册配置为包括令牌中的组声明**可以在门户的 "企业应用程序" 部分或使用应用程序注册部分中的应用程序清单来配置组声明。  若要配置应用程序清单中的组声明，请参阅下面的 "配置组属性的 Azure Active Directory 应用程序注册"。

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>使用 SSO 配置将组声明添加到 SAML 应用程序的令牌

若要为库或非库 SAML 应用程序配置组声明，请打开 "**企业应用程序**"，单击列表中的应用程序，选择 "**单一登录配置**"，然后选择 "**用户属性 & 声明**"。

单击 "**添加组声明**"  

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用单选按钮来选择哪些组应包括在令牌中

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 选择 | 说明 |
|----------|-------------|
| **所有组** | 发出安全组和通讯组列表和角色。  |
| **安全组** | 在组声明中发出用户所属的安全组 |
| **目录角色** | 如果为用户分配了目录角色，则会将其作为 "wids" 声明发出（不会发出组声明） |
| **分配给应用程序的组** | 仅发出显式分配给应用程序的组，并且该用户是其成员 |

例如，若要发出用户所属的所有安全组，请选择 "安全组"

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

若要使用从 Active Directory （而不是 Azure AD objectIDs）同步的 Active Directory 属性发出组，请从下拉端中选择所需的格式。 只有从 Active Directory 同步的组才会包含在声明中。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

若要仅发出分配给应用程序的组，请选择 **"分配给应用程序的组"**

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

分配给该应用程序的组将包含在令牌中。  用户所属的其他组将被忽略。  如果使用此选项，则不包括嵌套组，并且用户必须是分配给应用程序的组的直接成员。

若要更改分配给应用程序的组，请从 "**企业应用程序**" 列表中选择应用程序，然后在应用程序的左侧导航菜单中单击 "**用户和组**"。

有关管理应用程序的组分配的详细信息，请参阅[将用户或组分配到企业应用](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)。

### <a name="advanced-options"></a>高级选项

发出组声明的方式可以通过 "高级选项" 下的设置进行修改

自定义组声明的名称：如果选择此选项，则可以为组声明指定不同的声明类型。   在 "名称" 字段中输入声明类型，并在 "命名空间" 字段中输入声明的可选命名空间。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些应用程序要求组成员身份信息显示在 "role" 声明中。 您可以选择将用户的组作为角色发出，方法是选中 "发出组角色声明" 框。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用作为角色发出组数据的选项，则角色声明中将只显示组。  分配给用户的任何应用程序角色将不会出现在角色声明中。

### <a name="edit-the-group-claims-configuration"></a>编辑组声明配置

将组声明配置添加到 & 声明配置的用户属性后，添加组声明的选项将灰显。 若要更改组声明配置，请单击 "**其他声明**" 列表中的组声明。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>为组属性配置 Azure AD 应用程序注册

还可在[应用程序清单](../../active-directory/develop/reference-app-manifest.md)的 "[可选声明](../../active-directory/develop/active-directory-optional-claims.md)" 部分中配置组声明。

1. 在门户中 >Azure Active Directory > 应用程序注册->选择 "应用程序 >清单"

2. 通过更改 groupMembershipClaim 启用组成员身份声明

有效值是：

| 选择 | 说明 |
|----------|-------------|
| **一切** | 发出安全组、分发列表和角色 |
| **"SecurityGroup"** | 在组声明中发出用户所属的安全组 |
| **"DirectoryRole** | 如果为用户分配了目录角色，则会将其作为 "wids" 声明发出（不会发出组声明） |
| **"ApplicationGroup** | 仅发出显式分配给应用程序的组，并且该用户是其成员 |

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   默认情况下，将在组声明值中发出组 ObjectID。  若要将声明值修改为包含本地组特性，或者要将声明类型更改为角色，请按如下所示使用 OptionalClaims 配置：

3. 设置组名配置可选声明。

   如果希望令牌中的组包含本地 AD 组属性，请在 "可选声明" 部分中指定要应用的标记类型可选声明。  可以列出多个令牌类型：

   - OIDC ID 令牌的 idToken
   - OAuth/OIDC 访问令牌的 accessToken
   - SAML 令牌的 Saml2Token。

   > [!NOTE]
   > Saml2Token 类型将应用到 SAML1.1 和 SAML2.0 格式令牌

   对于每个相关的令牌类型，请修改组声明以在清单中使用 OptionalClaims 节。 OptionalClaims 架构如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 可选声明架构 | 值 |
   |----------|-------------|
   | **路径名** | 必须是“groups” |
   | **源程序** | 未使用。 省略或指定 null |
   | **或缺** | 未使用。 省略或指定 false |
   | **AdditionalProperties** | 其他属性的列表。  有效选项为“sam_account_name”、“dns_domain_and_sam_account_name”、“netbios_domain_and_sam_account_name”、“emit_as_roles” |

   在 additionalProperties 中，只需要指定“sam_account_name”、“dns_domain_and_sam_account_name”和“netbios_domain_and_sam_account_name”中的一个。  如果存在其中的多个选项，将使用第一个，并忽略其他所有选项。

   某些应用程序需要有关角色声明中的用户的组信息。  若要将声明类型从组声明更改为角色声明，请将“emit_as_roles”添加到附加属性。  组值将在角色声明中发出。

   > [!NOTE]
   > 如果使用“emit_as_roles”，则分配了用户的任何已配置应用程序角色不会显示在角色声明中

### <a name="examples"></a>示例

以 dnsDomainName\SAMAccountName 格式将组作为 OAuth 访问令牌中的组名发出

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

若要发出以 netbiosDomain\samAccountName 格式返回的组名作为 SAML 和 OIDC ID 令牌中的角色声明：

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>后续步骤

[向企业应用分配用户或组](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[配置角色声明](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
