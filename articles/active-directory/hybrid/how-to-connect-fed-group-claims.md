---
title: 为具有 Azure 活动目录的应用程序配置组声明 |微软文档
description: 有关如何配置组声明以便与 Azure AD 一起使用的信息。
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
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408396"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>为具有 Azure 活动目录的应用程序配置组声明（公共预览）

Azure 活动目录可以为用户提供令牌中的组成员身份信息，以便在应用程序中使用。  支持两种主要模式：

- 由其 Azure 活动目录对象标识符 （OID） 属性标识的组（一般可用）
- 由 sAMAccountName 或"组SID"属性标识的活动目录 （AD） 同步组和用户（公共预览）

> [!IMPORTANT]
> 对于此预览功能，需要注意许多注意事项：
>
>- 支持使用从本地同步的 sAMAccountName 和安全标识符 （SID） 属性，旨在启用从 AD FS 和其他标识提供程序移动现有应用程序。 在 Azure AD 中管理的组不包含发出这些声明所需的属性。
>- 在较大的组织中，用户作为其成员的组数可能超过 Azure 活动目录将添加到令牌的限制。 SAML 令牌的 150 个组和 JWT 的 200 个组。 这可能导致不可预知的结果。 如果用户拥有大量组成员身份，我们建议使用选项将声明中发出的组限制为应用程序的相关组。  
>- 对于新的应用程序开发，或者在可以为其配置应用程序以及不需要嵌套组支持的情况下，我们建议应用内授权基于应用程序角色而不是组。  这将限制需要进入令牌的信息量，更安全，并将用户分配与应用配置分开。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>组从 AD FS 和其他标识提供程序迁移的应用程序的声明

许多配置为使用 AD FS 进行身份验证的应用程序依赖于 Windows AD 组属性形式的组成员身份信息。   这些属性是组 sAMAccountName（可能是限定的子域名）或 Windows 组安全标识符 （GroupSID）。  当应用程序与 AD FS 联合时，AD FS 使用令牌组函数检索用户的组成员身份。

从 AD FS 移动的应用需要以相同格式的声明。 组和角色声明可能从 Azure 活动目录中发出，其中包含域限定的 sAMAccountName 或从活动目录而不是组的 Azure 活动目录对象 ID 同步的 GroupSID。

组声明的支持格式包括：

- **Azure 活动目录组对象 Id（** 适用于所有组）
- **sAMAccount名称**（适用于从活动目录同步的组）
- **NetbiosDomain\sAMAccount名称**（适用于从活动目录同步的组）
- **DNS域名\sAM帐户名称**（适用于从活动目录同步的组）
- **在本地组安全标识符**（适用于从活动目录同步的组）

> [!NOTE]
> sAMAccountName 和本地组 SID 属性仅在从活动目录同步的组对象上可用。   它们不适用于在 Azure 活动目录或 Office365 中创建的组。   在 Azure 活动目录中配置为同步的本地组属性的应用程序仅为同步组获取它们。

## <a name="options-for-applications-to-consume-group-information"></a>应用程序使用组信息的选项

应用程序可以调用 MS Graph 组终结点以获取经过身份验证的用户的组信息。 此调用可确保用户是 其成员的所有组都可用，即使涉及大量组也是如此。  然后，组枚举独立于令牌大小限制。

但是，如果现有应用程序希望通过声明使用组信息，则可以使用许多不同的声明格式配置 Azure 活动目录。  请考虑以下选项：

- 在将组成员身份用于应用程序内授权时，最好使用组 ObjectID。 组 ObjectID 不可变且在 Azure 活动目录中唯一，适用于所有组。
- 如果使用本地组 sAMAccountName 进行授权，请使用具有域限定的名称;如果使用本地组 sAMAccountName 进行授权，请使用具有域限定的名称; 名字发生冲突的可能性较小。 sAMAccountName 在 Active Directory 域中可能是唯一的，但如果多个活动目录域与 Azure 活动目录租户同步，则多个组可能具有相同的名称。
- 请考虑使用[应用程序角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)在组成员身份和应用程序之间提供一层间接。   然后，应用程序根据令牌中的角色蛤做出内部授权决策。
- 如果应用程序配置为获取从 Active Directory 同步的组属性，并且组不包含这些属性，则声明中不会包含该属性。
- 令牌中的组声明包括嵌套组，除非使用选项将组声明限制为分配给应用程序的组。  如果用户是 GroupB 的成员，而 GroupB 是 GroupA 的成员，则该用户的组声明将同时包含 GroupA 和 GroupB。 当组织的用户拥有大量组成员身份时，令牌中列出的组数可能会增加令牌大小。  Azure 活动目录将它在令牌中发出的 SAML 断言中的组数限制为 150，JWT 将限制为 200 个。  如果用户是更多组的成员，则省略组，并改为包含指向 Graph 终结点以获取组信息的链接。

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>使用从活动目录同步的组属性的先决条件

如果使用 ObjectId 格式，则可以以任何组的令牌形式发出组成员声明。 要以组 ObjectId 以外的格式使用组声明，必须使用 Azure AD 连接从活动目录同步组。

配置 Azure 活动目录以发出活动目录组的组名称有两个步骤。

1. **从活动目录同步组名称**在 Azure 活动目录可以在组或角色声明中发出组名称或内部部署组 SID 之前，需要从活动目录同步所需的属性。  您必须运行 Azure AD 连接版本 1.2.70 或更高版本。   早期版本的 Azure AD Connect 比 1.2.70 将同步来自活动目录的组对象，但不会包括所需的组名称属性。  升级到当前版本。

2. **在 Azure 活动目录中配置应用程序注册以在令牌中包括组声明**组声明可以在门户的企业应用程序部分中配置，也可以在应用程序注册部分中使用应用程序清单。  要在应用程序清单中配置组声明，请参阅下面的"配置组属性的 Azure 活动目录应用程序注册"。

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>使用 SSO 配置将组声明添加到 SAML 应用程序的令牌

要为库或非库 SAML 应用程序配置组声明，请打开**企业应用程序**，单击列表中的应用程序，选择**单一登录配置**，然后选择 **"用户属性&声明**"。

单击"**添加组声明**"  

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用单选按钮选择令牌中应包含哪些组

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 选项 | 描述 |
|----------|-------------|
| **所有组** | 发出安全组、通讯组列表和角色。  |
| **安全组** | 发出用户是组声明的成员的安全组 |
| **目录角色** | 如果用户被分配了目录角色，则这些角色将作为"wids"声明发出（不会发出组声明） |
| **分配给应用程序的组** | 仅发出显式分配给应用程序的组，用户是 |

例如，要发出用户是的所有安全组，请选择安全组

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

要使用从活动目录而不是 Azure AD 对象 IT 同步的活动目录属性发出组，请从下拉列表中选择所需的格式。 声明中仅包括从活动目录同步的组。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

要仅发出分配给应用程序的组，请选择**分配给应用程序的组**

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

分配给应用程序的组将包含在令牌中。  用户是其成员的其他组将被省略。  使用此选项，不包括嵌套组，用户必须是分配给应用程序的组的直接成员。

要更改分配给应用程序的组，请从 **"企业应用程序"** 列表中选择应用程序，然后单击应用程序左侧导航菜单中的 **"用户和组**"。

有关管理组分配到应用程序的详细信息，请参阅文档[将用户或组分配给企业应用](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)。

### <a name="advanced-options"></a>高级选项

可以通过"高级"选项下的设置修改组声明的发出方式

自定义组声明的名称：如果选中，可以为组声明指定不同的声明类型。   在"名称"字段中输入声明类型，在命名空间字段中输入声明的可选命名空间。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些应用程序要求组成员身份信息显示在"角色"索赔中。 通过选中"Emit 对角色声明的 Emit"框，可以选择将用户的组作为角色发出。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用角色发出组数据的选项，则角色声明中只会显示组。  用户分配的任何应用程序角色将不会显示在角色声明中。

### <a name="edit-the-group-claims-configuration"></a>编辑组声明配置

将组声明配置添加到用户属性&声明配置后，添加组声明的选项将灰显。 要更改组声明配置，请单击 **"其他索赔**"列表中的组声明。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>为组属性配置 Azure AD 应用程序注册

组声明也可以在[应用程序清单](../../active-directory/develop/reference-app-manifest.md)的[可选声明](../../active-directory/develop/active-directory-optional-claims.md)部分进行配置。

1. 在门户中 ->Azure 活动目录 ->应用程序注册->选择应用程序->清单

2. 通过更改组成员声明启用组成员声明

有效值是：

| 选项 | 描述 |
|----------|-------------|
| **"全部"** | 发出安全组、通讯组列表和角色 |
| **"SecurityGroup"** | 发出用户是组声明的成员的安全组 |
| **"目录角色** | 如果用户被分配了目录角色，则这些角色将作为"wids"声明发出（不会发出组声明） |
| **"应用程序组** | 仅发出显式分配给应用程序的组，用户是 |

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   默认情况下，将在组声明值中发出组 ObjectID。  若要将声明值修改为包含本地组特性，或者要将声明类型更改为角色，请按如下所示使用 OptionalClaims 配置：

3. 设置组名配置可选声明。

   如果希望令牌中的组包含本地 AD 组属性，请在可选声明部分中指定应应用于哪个令牌类型可选声明。  可以列出多个令牌类型：

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

   | 可选声明架构 | “值” |
   |----------|-------------|
   | **名字：** | 必须是“groups” |
   | **源：** | 未使用。 省略或指定 null |
   | **基本：** | 未使用。 省略或指定 false |
   | **additionalProperties：** | 其他属性的列表。  有效选项为“sam_account_name”、“dns_domain_and_sam_account_name”、“netbios_domain_and_sam_account_name”、“emit_as_roles” |

   在 additionalProperties 中，只需要指定“sam_account_name”、“dns_domain_and_sam_account_name”和“netbios_domain_and_sam_account_name”中的一个。  如果存在其中的多个选项，将使用第一个，并忽略其他所有选项。

   某些应用程序需要有关角色声明中的用户的组信息。  若要将声明类型从组声明更改为角色声明，请将“emit_as_roles”添加到附加属性。  组值将在角色声明中发出。

   > [!NOTE]
   > 如果使用“emit_as_roles”，则分配了用户的任何已配置应用程序角色不会显示在角色声明中

### <a name="examples"></a>示例

以 dnsDomainName_SAMAccountName 格式在 OAuth 访问令牌中将组名称作为组名称发出

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

要发出要以 netbiosDomain_samAccountName 格式返回的组名称，作为 SAML 和 OIDC ID 令牌中的角色声明：

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
