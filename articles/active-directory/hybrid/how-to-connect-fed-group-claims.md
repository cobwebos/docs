---
title: 配置与 Azure Active Directory 组声明的应用程序 |Microsoft Docs
description: 若要了解如何使用 Azure AD 配置为使用的组声明。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 622a3ce0f80bd09bd09fa7ff097f68155318142d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351165"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>使用 Azure Active Directory （公共预览版） 配置应用程序的组声明

Azure Active Directory 可以提供在应用程序中使用的令牌中的用户组成员身份信息。  支持两种主要模式：

- 通过其 Azure Active Directory 对象标识符 (OID) （正式版） 标识的组
- Active Directory (AD) 同步组和用户 （公共预览版） 通过 SAMAccountName 或 GroupSID 标识的组

> [!Note]
> 可从 AD FS 迁移现有应用程序，设计使用的名称和在本地安全标识符 (Sid) 的支持。    在 Azure AD 中管理组不包含用于发出这些声明所必要的属性。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-idps"></a>应用程序在从 AD FS 和其他 Idp 迁移的组声明

许多应用程序配置为使用 AD FS 进行身份验证依赖于 Windows AD 组属性的窗体中的组成员身份信息。   这些特性是组 SAMAccountName，可能是由域限定的名称或 Windows 组 SID。  当应用程序已与 AD FS 联合时，AD FS 使用 TokenGroups 函数检索用户的组成员身份。

若要匹配的应用将接收从 AD FS 令牌，可能会发出组和角色声明的域限定 SAMAccountName 而不是组的 Azure Active Directory 对象 Id。

组声明支持的格式为：

- **Azure Active Directory GroupObjectId** （适用于所有组）
- **SAMAccountName** （适用于从 Active Directory 同步的组）
- **NetbiosDomain\samAccountName** （适用于从 Active Directory 同步的组）
- **DNSDomainName\samAccountName** （适用于从 Active Directory 同步的组）

> [!NOTE]
> SAMAccountName 和 OnPremisesGroupSID 属性才可在从 Active Directory 同步的组对象上。   他们不是可在 Azure Active Directory 或 office 365 中创建的组上。   应用程序依赖于在本地组属性获取它们仅同步组。

## <a name="options-for-applications-to-consume-group-information"></a>应用程序组信息使用的选项

若要获取组的信息的应用程序的一种方法是调用 Graph 组终结点，以便检索经过身份验证的用户的组成员身份。 此调用可确保所有用户的组的成员都可用，即使有涉及大量的组和应用程序必须枚举所有用户组的成员。  然后，组枚举是独立于令牌的大小限制。

但是，如果希望使用通过声明的令牌中收到的组信息已将现有应用程序，可以配置 Azure Active Directory 使用多种不同的声明的应用程序的需求。  请考虑以下选项：

- 使用组成员身份进行时 （无论组成员身份获取从令牌或从关系图） 应用程序授权目的，最好是使用组的 ObjectID，这是不可变和 Azure Active Directory 中唯一可用的所有组.
- 如果使用 SAMAccountName 的组进行授权，使用限定的域名; 具有更少的情况下出现可能性没有名称冲突。 上其自己的 SAMAccountName 可能中是唯一的 Active Directory 域，但如果多个 Active Directory 域同步与 Azure Active Directory 租户则可能要具有相同名称的多个组。
- 请考虑使用[应用程序角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)提供的组成员身份和应用程序之间的间接层。   然后，应用程序做出基于令牌中的角色 clams 的内部授权决策。
- 如果应用程序配置为从 Active Directory 获取同步组特性，并且每个组不包含这些属性，它不会包含在声明中。
- 在令牌中的组声明包括嵌套的组。   如果用户是 GroupB 的成员并且 GroupB 是组的成员，然后该用户的组声明将包含组此和 GroupB。 具有嵌套组的使用率很高的组织和具有大量的组成员身份的用户令牌中列出的组数量可能会增长令牌大小。   Azure Active Directory 限制它会发出到 SAML 断言，150，用 200 表示 JWT 令牌中的组数。

> 使用组属性从 Active Directory 同步的先决条件： 必须从使用 Azure AD Connect 的 Active Directory 同步组。

有两个步骤来配置 Azure Active Directory 发出的 Active Directory 组的组名称。

1. **同步组名称从 Active Directory**之前 Azure Active Directory 可发出组名称或内部部署组上的组或角色中的 SID 声明，需要从 Active Directory 同步所需的属性。  您必须运行 Azure AD Connect 版本 1.2.70 或更高版本。   在版本 1.2.70 之前 Azure AD Connect 将同步个组对象从 Active Directory，但默认情况下不包括所需的组名称属性。  应升级到最新版本。

2. **要包含在令牌中的组声明的 Azure Active Directory 中配置的应用程序注册**组声明可以是配置库或非库 SAML SSO 应用程序，在门户的企业应用程序部分中的其中任一或使用应用程序清单中的应用程序注册部分。  若要在应用程序清单请参阅下面的"配置属性进行分组的 Azure Active Directory 应用程序注册"中配置组声明。

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>配置 SAML 应用程序使用 SSO 配置的组声明

若要配置的库或非库 SAML 应用程序的组声明，打开企业应用程序，单击列表中的应用程序并选择单一登录配置。

选择"在令牌中返回的组"旁边的编辑图标

![声明用户界面](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用单选按钮来选择应在令牌中包含哪些组

![声明用户界面](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 选项 | 描述 |
|----------|-------------|
| **所有组** | 发出安全组和通讯组列表。   它还会导致为用户分配要在 wids 声明中，在发出的目录角色和用户分配要在角色声明中发出任何应用程序角色。 |
| **安全组** | 发出用户所在的组声明中的安全组 |
| **通讯组列表** | 发出用户所在的通讯组列表 |
| **目录角色** | 如果将用户分配目录角色时发出作为 wids 声明 （不会发出声明的组） |

例如，若要发出用户所属的所有安全组，请选择安全组

![声明用户界面](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

### <a name="advanced-options"></a>高级选项

可以通过在高级选项下的设置修改发出组声明的方式

自定义组声明的名称：如果选择，可以为组声明指定不同的声明类型。   在名称字段和可选的命名空间中的声明类型输入命名空间字段中的声明。

![声明用户界面](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

若要发出使用 Active Directory 组属性而不是 Azure AD Objectid 复选 Return 组名称而不是 Id 为框中，并从下拉列表中选择的格式。  这将在声明中的对象 ID 替换包含组名称的字符串值。   只有从 Active Directory 同步的组将包含在声明中。

![声明用户界面](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些应用程序需要组成员身份信息显示在角色声明中。 通过选中发出角色声明的组复选框，可选择性地为角色发出用户的组。

![声明用户界面](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用以发出以角色身份的组数据的选项，则只有组会在角色声明中。  为用户分配任何应用程序角色不会出现在角色声明。

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>配置 Azure AD 应用程序注册的组属性

也可以用配置组声明[可选声明](../../active-directory/develop/active-directory-optional-claims.md)一部分[应用程序清单](../../active-directory/develop/reference-app-manifest.md)。

1. 在门户中-> Azure Active Directory-> 应用程序注册-> 选择应用程序-> 清单

2. 通过更改 groupMembershipClaim 启用组成员身份声明

   有效值为：

   - "全部"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   默认情况下将在组中发出组 Objectid 声明值。  若要修改的声明值以包含本地组属性，或声明类型更改为角色，请按如下所示使用 OptionalClaims 配置：

3. 设置组名称配置可选声明。

   如果你想向令牌中的组要包含在本地 AD 组属性的可选声明部分中指定的标记类型的可选声明应应用于、 请求的可选声明和所需的任何其他属性的名称。  可以列出多个令牌类型：

   - OIDC ID 令牌的 idToken
   - accessToken OAuth/OIDC 访问令牌
   - SAML 令牌 Saml2Token。

   > [!NOTE]
   > Saml2Token 类型适用于 SAML1.1 和 SAML2.0 格式标记

   对于每个相关的标记类型，修改要使用 OptionalClaims 部分在清单中的组声明。 OptionalClaims 架构如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 可选声明架构 | Value |
   |----------|-------------|
   | **名称：** | 必须是"组" |
   | **源：** | 不使用。 省略或指定 null |
   | **重要：** | 不使用。 省略或指定 false |
   | **additionalProperties:** | 其他属性的列表。  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

   在只有一个"sam_account_name"，"dns_domain_and_sam_account_name"additionalProperties"netbios_domain_and_sam_account_name"是必需的。  如果存在多个，使用第一个和任何其他人所忽略。

   某些应用程序需要有关角色声明中的用户组信息。  若要更改要从组声明与角色声明，将"emit_as_roles"添加到附加属性的声明类型。  将角色声明中发出组值。

   > [!NOTE]
   > 如果使用"emit_as_roles"任何应用程序角色配置为用户分配将不会出现在角色声明

### <a name="examples"></a>示例

发出组作为 dnsDomainName\SAMAccountName 格式中的 OAuth 访问令牌中的组名称

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

若要发出角色声明在 SAML 和 OIDC ID 令牌中作为返回 netbiosDomain\samAccountName 格式中的组名称：

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

[什么是混合标识？](whatis-hybrid-identity.md)