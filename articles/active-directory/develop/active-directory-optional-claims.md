---
title: 向 Azure AD 应用提供可选声明
titleSuffix: Microsoft identity platform
description: 如何将自定义或附加声明添加到 (JWT) Microsoft 标识平台颁发的令牌的 SAML 2.0 和 JSON Web 令牌。
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: e53cf38c9544884caddfdf03c2615217c49ec3d0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068720"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>如何：向应用程序提供可选声明

应用程序开发人员可以在其 Azure AD 应用程序中使用可选声明来指定他们希望在发送到其应用程序的令牌中使用哪些声明。

使用可选声明可以：

- 选择要包含在应用程序令牌中的附加声明。
- 更改 Microsoft 标识平台在令牌中返回的某些声明的行为。
- 添加和访问应用程序的自定义声明。

如需标准声明的列表，请参阅[访问令牌](access-tokens.md)和 [id_token](id-tokens.md) 声明文档。

尽管在 v1.0 和 v2.0 格式令牌以及 SAML 令牌中都支持可选声明，但在从 v1.0 移动到 v2.0 时，它们提供了其最大值。 [v2.0 Microsoft 标识平台终结点](./v2-overview.md)的目标之一是减小令牌大小，以确保客户端获得最佳性能。 因此，以前包含在访问令牌和 ID 令牌中的多个声明不再在 v2.0 令牌中提供，必须根据应用程序专门请求这些声明。

**表 1：适用性**

| 帐户类型               | v1.0 令牌 | v2.0 令牌 |
|----------------------------|-------------|-------------|
| Microsoft 个人帐户 | 空值         | 支持   |
| Azure AD 帐户           | 支持   | 支持   |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 和 v2.0 可选声明集

下面列出了默认可对应用程序使用的可选声明集。 若要为应用程序添加自定义可选声明，请参阅下面的[目录扩展](#configuring-directory-extension-optional-claims)。 在向**访问令牌**添加声明时，这些声明应用到应用程序 (Web API) 请求的访问令牌，而不是应用程序请求的声明。 无论客户端如何访问你的 API，正确的数据都存在于用于对该 API 进行身份验证的访问令牌中。

> [!NOTE]
> 其中的大多数声明可包含在 v1.0 和 v2.0 令牌的 JWT 中，但不可包含在 SAML 令牌中，“令牌类型”列中指明的声明除外。 使用者帐户支持部分在“用户类型”列中标记的此类声明。  列出的许多声明不适用于使用者用户（他们没有租户，因此 `tenant_ctry` 没有值）。

**表 2：v1.0 和 v2.0 可选声明集**

| 名称                       |  说明   | 令牌类型 | 用户类型 | 说明  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | 用户上次进行身份验证的时间。 请参阅 OpenID Connect 规范。| JWT        |           |  |
| `tenant_region_scope`      | 资源租户的区域 | JWT        |           | |
| `sid`                      | 会话 ID，用于基于会话的用户注销。 | JWT        |  个人帐户和 Azure AD 帐户。   |         |
| `verified_primary_email`   | 源自用户的 PrimaryAuthoritativeEmail      | JWT        |           |         |
| `verified_secondary_email` | 源自用户的 SecondaryAuthoritativeEmail   | JWT        |           |        |
| `vnet`                     | VNET 说明符信息。 | JWT        |           |      |
| `fwd`                      | IP 地址。| JWT    |   | 添加请求方客户端（如果位于 VNET 中）的原始 IPv4 地址 |
| `ctry`                     | 用户所在国家/地区 | JWT、SAML |  | Azure AD 将返回 `ctry` 可选声明（如果存在），并且该字段的值为标准的双字母国家/地区代码，例如 FR、JP、SZ 等。 |
| `tenant_ctry`              | 资源租户所在的国家/地区 | JWT | | 与 `ctry` 管理员在租户级别上设置的除外。 还必须是标准的双字母值。 |
| `xms_pdl`             | 首选数据位置   | JWT | | 对于多地域租户，首选数据位置是显示用户所在地理区域的由三个字母组成的代码。 有关详细信息，请参阅[有关首选数据位置的 Azure AD Connect 文档](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md)。<br/>例如：`APC` 表示“亚太”。 |
| `xms_pl`                   | 用户首选语言  | JWT ||用户的首选语言（如果已设置）。 在来宾访问方案中，源自其主租户。 已格式化 LL-CC（“zh-cn”）。 |
| `xms_tpl`                  | 租户首选语言| JWT | | 资源租户的首选语言（如果已设置）。 已格式化 LL（“en”）。 |
| `ztdid`                    | 零接触部署 ID | JWT | | 用于 [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-10-autopilot) 的设备标识 |
| `email`                    | 此用户的可寻址电子邮件（如果此用户有）。  | JWT、SAML | MSA、Azure AD | 如果用户是租户中的来宾，则默认包含此值。  对于托管用户（租户内部的用户），必须通过此可选声明进行请求，或者仅在 v2.0 上使用 OpenID 范围进行请求。  对于托管用户，必须在 [Office 管理门户](https://portal.office.com/adminportal/home#/users)中设置电子邮件地址。|
| `acct`                | 租户中的用户帐户状态 | JWT、SAML | | 如果用户是租户的成员，则该值为 `0`。 如果他们是来宾，则该值为 `1`。 |
| `groups`| 组声明的可选格式 |JWT、SAML| |与[应用程序清单](reference-app-manifest.md)中的 GroupMembershipClaims 设置（也是必需的）结合使用。 有关详细信息，请参阅下面的[组声明](#configuring-groups-optional-claims)。 有关组声明的详细信息，请参阅[如何配置组声明](../hybrid/how-to-connect-fed-group-claims.md)
| `upn`                      | UserPrincipalName | JWT、SAML  |           | 尽管会自动包含此声明，但可以将它指定为可选声明，以附加额外的属性，在来宾用例中修改此声明的行为。  |
| `idtyp`                    | 令牌类型   | JWT 访问令牌 | 特别之处：仅在仅限应用的访问令牌中 |  当令牌为仅限应用的令牌时，值为 `app`。 这是 API 确定令牌是应用令牌还是应用+用户令牌最准确的方法。|

## <a name="v20-specific-optional-claims-set"></a>特定于 v2.0 的可选声明集

这些声明始终包含在 v1.0 Azure AD 令牌中，但除非提出请求，否则不会包含在 v2.0 令牌中。 这些声明仅适用于 JWT（ID 令牌和访问令牌）。

**表 3：仅限 v2.0 的可选声明**

| JWT 声明     | 名称                            | 说明                                | 说明 |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP 地址                      | 客户端从中登录的 IP 地址。   |       |
| `onprem_sid`  | 本地安全标识符 |                                             |       |
| `pwd_exp`     | 密码过期时间        | 密码过期的日期时间。 |       |
| `pwd_url`     | 更改密码 URL             | 用户更改密码时可以访问的 URL。   |   |
| `in_corp`     | 企业网络内部        | 表示客户端是否从企业网络登录。 如果不是，则不包括该声明。   |  以 MFA 中的[可信 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) 设置为基础。    |
| `family_name` | 姓氏                       | 根据用户对象中的定义提供用户的姓氏。 <br>"family_name":"Miller" | 在 MSA 和 Azure AD 中受支持。 需要 `profile` 范围。   |
| `given_name`  | 名字                      | 根据用户对象中的设置提供用户的名字和“姓氏”。<br>"given_name":"Frank"                   | 在 MSA 和 Azure AD 中受支持。  需要 `profile` 范围。 |
| `upn`         | 用户主体名称 | 可以与 username_hint 参数一起使用的用户标识符。  不是用户的持久标识符，不应当用于关键数据。 | 有关声明配置，请参阅下面的[附加属性](#additional-properties-of-optional-claims)。 需要 `profile` 范围。|

### <a name="additional-properties-of-optional-claims"></a>可选声明的附加属性

可以配置某些可选声明来更改声明的返回方式。 这些附加属性主要用于帮助迁移具有不同数据预期的本地应用程序（例如，`include_externally_authenticated_upn_without_hash` 可帮助迁移无法处理 UPN 中的井号标记 (`#`) 的客户端）

**表 4：用于配置可选声明的值**

| 属性名称  | 附加属性名称 | 说明 |
|----------------|--------------------------|-------------|
| `upn`          |                          | 可用于 SAML 和 JWT 响应，以及 v1.0 和 v2.0 令牌。 |
|                | `include_externally_authenticated_upn`  | 包含资源租户中存储的来宾 UPN。 例如： `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | 同上，不过，井号标记 (`#`) 已替换为下划线 (`_`)，例如 `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>附加属性示例

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

此 OptionalClaims 对象会导致返回到客户端的 ID 令牌包含一个 upn 声明及其他主租户和资源租户信息。 仅当用户是租户中的来宾（使用不同的 IDP 进行身份验证）时，才会更改令牌中的 `upn` 声明。

## <a name="configuring-optional-claims"></a>配置可选声明

> [!IMPORTANT]
> 访问令牌始终使用资源清单而不是客户端生成。  因此，在请求 `...scope=https://graph.microsoft.com/user.read...` 中，资源是 Microsoft Graph API。  因此，访问令牌是使用 Microsoft Graph API 清单而不是客户端清单创建的。  更改应用程序的清单从不会导致 Microsoft Graph API 的令牌看起来有何不同。  为了验证 `accessToken` 更改是否有效，请为你的应用程序请求一个令牌，而不是其他应用。

可以通过 UI 或应用程序清单来配置应用程序的可选声明。

1. 转到 [Azure 门户](https://portal.azure.com)。 搜索并选择“Azure Active Directory”。
1. 从“管理”部分中选择“应用注册” 。
1. 在列表中选择要为其配置可选声明的应用程序。

**通过 UI 配置可选声明：**

[![演示如何使用 UI 配置可选声明](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. 从“管理”部分中选择“令牌配置” 。
1. 选择“添加可选声明”。
1. 选择要配置的令牌类型。
1. 选择要添加的可选声明。
1. 选择“添加”  。

**通过应用程序清单配置可选声明：**

[![演示如何使用应用清单配置可选声明](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. 在“管理”部分，选择“清单”。  此时会打开一个基于 Web 的清单编辑器，可在其中编辑清单。 （可选）可以选择“下载”并在本地编辑清单，然后使用“上传”将清单重新应用到应用程序。  有关应用程序清单的详细信息，请参阅[了解 Azure AD 应用程序清单](reference-app-manifest.md)一文。

    以下应用程序清单条目将 auth_time、ipaddr 和 upn 可选声明添加到 ID、访问和 SAML 令牌。

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. 完成后，选择“保存”。 现在，指定的可选声明将包含在应用程序的令牌中。

### <a name="optionalclaims-type"></a>OptionalClaims 类型

声明应用程序请求的可选声明。 应用程序可以配置为要在它能够从安全令牌服务收到的每种令牌（共三种，即 ID 令牌、访问令牌和 SAML 2 令牌）中返回可选声明。 应用程序可以配置为要在每种令牌类型中返回一组不同的可选声明。 Application 实体的 OptionalClaims 属性是一个 OptionalClaims 对象。

**表 5：OptionalClaims 类型属性**

| 名称          | 类型                       | 说明                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | 集合 (OptionalClaim) | 在 JWT ID 令牌中返回的可选声明。     |
| `accessToken` | 集合 (OptionalClaim) | 在 JWT 访问令牌中返回的可选声明。 |
| `saml2Token`  | 集合 (OptionalClaim) | 在 SAML 令牌中返回的可选声明。       |

### <a name="optionalclaim-type"></a>OptionalClaim 类型

包含与应用程序或服务主体关联的可选声明。 [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) 类型的 IdToken、accessToken 和 saml2Token 属性是一个 OptionalClaim 集合。
如果特定的声明支持这样做，则还可以使用 AdditionalProperties 字段修改 OptionalClaim 的行为。

**表 6：OptionalClaim 类型属性**

| 名称                   | 类型                    | 说明                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 可选声明的名称。                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | 声明的源（目录对象）。 扩展属性提供预定义声明和用户定义的声明。 如果源值为 null，则声明是预定义的可选声明。 如果源值为 user，则 name 属性中的值是来自用户对象的扩展属性。 |
| `essential`            | Edm.Boolean             | 如果值为 true，则必须使用客户端指定的声明，以确保为最终用户请求的特定任务提供顺利的授权体验。 默认值为 false。                                                                                                                 |
| `additionalProperties` | 集合 (Edm.String) | 声明的附加属性。 如果此集合中存在某个属性，该属性将修改 name 属性中指定的可选声明的行为。                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>配置目录扩展可选声明

除了标准的可选声明集外，还可以将令牌配置为包括扩展。 有关详细信息，请参阅 [Microsoft Graph extensionProperty 文档](/graph/api/resources/extensionproperty?view=graph-rest-1.0)。

可选声明不支持架构和开放扩展，仅支持 AAD-Graph 样式的目录扩展。 使用此功能可以附加应用可以使用的附加用户信息 – 例如，用户设置的附加标识符或重要配置选项。 请参阅本页底部的示例。

> [!NOTE]
> 目录架构扩展功能只能在 AAD 中使用。 如果应用程序清单请求自定义扩展，而 MSA 用户登录到你的应用，则不会返回这些扩展。

### <a name="directory-extension-formatting"></a>目录扩展格式

使用应用程序清单配置目录扩展可选声明时，请使用扩展的完整名称（格式为：`extension_<appid>_<attributename>`）。 `<appid>` 必须与请求该声明的应用程序的 ID 相匹配。

在 JWT 中，将使用以下命名格式发出这些声明：`extn.<attributename>`。

在 SAML 令牌中，将使用以下 URI 格式发出这些声明：`http://schemas.microsoft.com/identity/claims/extn.<attributename>`。

## <a name="configuring-groups-optional-claims"></a>配置组可选声明

   > [!NOTE]
   > 为从本地同步的用户和组发出组名的功能目前为公共预览版。

本部分介绍可选声明下的配置选项，这些选项可将组声明中使用的组特性从默认的组 objectID 更改为从本地 Windows Active Directory 同步的特性。 可以通过 UI 或应用程序清单来配置应用程序的组可选声明。

> [!IMPORTANT]
> 有关更多详细信息，包括有关本地属性中组声明的公共预览版的重要注意事项，请参阅[通过 Azure AD 配置应用程序的组声明](../hybrid/how-to-connect-fed-group-claims.md)。

**通过 UI 配置组可选声明：**

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 通过身份验证后，在页面右上角选择 Azure AD 租户
1. 在左侧菜单中，选择“Azure Active Directory”
1. 在“管理”部分下，选择“应用注册” 
1. 在列表中选择要为其配置可选声明的应用程序
1. 在“管理”部分下，选择“令牌配置” 
1. 选择“添加组声明”
1. 选择要返回的组类型（“所有组”、“SecurityGroup”或“DirectoryRole”）。   “所有组”选项包括“SecurityGroup”、“DirectoryRole”和“DistributionList”   
1. 可选：选择特定的令牌类型属性，将组声明值修改为包含本地组特性，或将声明类型更改为角色
1. 选择“保存”

**通过应用程序清单配置组可选声明：**

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 通过身份验证后，在页面右上角选择 Azure AD 租户
1. 在左侧菜单中，选择“Azure Active Directory”
1. 在列表中选择要为其配置可选声明的应用程序
1. 在“管理”部分下，选择“清单” 
1. 使用清单编辑器添加以下条目：

   有效值为：

   - “所有”（此选项包括 SecurityGroup、DirectoryRole 和 DistributionList）
   - "SecurityGroup"
   - "DirectoryRole"

   例如：

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   默认情况下，将在组声明值中发出组 ObjectID。  若要将声明值修改为包含本地组特性，或者要将声明类型更改为角色，请按如下所示使用 OptionalClaims 配置：

1. 设置组名配置可选声明。

   如果希望令牌中的组包含“可选声明”节中的本地 AD 组特性，请指定要应用到的令牌类型可选声明、请求的可选声明的名称，以及所需的任何其他属性。  可以列出多个令牌类型：

   - OIDC ID 令牌的 idToken
   - OAuth 访问令牌的 accessToken
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

   | 可选声明架构 | Value |
   |----------|-------------|
   | **name：** | 必须是“groups” |
   | **source：** | 未使用。 省略或指定 null |
   | **essential：** | 未使用。 省略或指定 false |
   | **additionalProperties：** | 附加属性列表。  有效选项为“sam_account_name”、“dns_domain_and_sam_account_name”、“netbios_domain_and_sam_account_name”、“emit_as_roles” |

   在 additionalProperties 中，只需要指定“sam_account_name”、“dns_domain_and_sam_account_name”和“netbios_domain_and_sam_account_name”中的一个。  如果存在多个，则将使用第一个，而忽略其他。

   某些应用程序需要角色声明中有关用户的组信息。  要将声明类型从组声明更改为角色声明，请将“emit_as_roles”添加到附加属性。  组值将在角色声明中发出。

   > [!NOTE]
   > 如果使用“emit_as_roles”，则分配了用户的任何已配置应用程序角色不会显示在角色声明中

**示例：**

1) 在 OAuth 访问令牌中以 dnsDomainName\sAMAccountName 格式的组名发出组

    **UI 配置：**

    [![演示如何使用 UI 配置可选声明](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **应用程序清单条目：**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) 发出要以 netbiosDomain\sAMAccountName 格式返回的组名，作为 SAML 和 OIDC ID 令牌中的角色声明

    **UI 配置：**

    [![演示如何使用 UI 配置可选声明](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **应用程序清单条目：**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>可选声明示例

本部分通过一个场景来演练如何对应用程序使用可选声明功能。
可以使用多个选项来更新应用程序标识配置中的属性，以启用和配置可选声明：

- 可以使用“令牌配置”UI（请参阅下面的示例）
- 可以使用“清单”（请参阅下面的示例）。 请先阅读[了解 Azure AD 应用程序清单文档](./reference-app-manifest.md)中的清单简介。
- 也可以编写使用 [Microsoft Graph API](/graph/use-the-api?context=graph%2fapi%2f1.0&view=graph-rest-1.0) 的应用程序来更新应用程序。 Microsoft Graph API 参考指南中的 [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) 类型可帮助你配置可选声明。

**示例：**

以下示例使用“令牌配置”UI 和“清单”将可选声明添加到用于应用程序的访问令牌、ID 令牌和 SAML 令牌 。 不同的可选声明将添加到应用程序可以接收的每种令牌：

- ID 令牌现在会包含联合用户的完整格式 UPN (`<upn>_<homedomain>#EXT#@<resourcedomain>`)。
- 其他客户端请求此应用程序的访问令牌现在将包含 auth_time 声明
- SAML 令牌现在会包含 skypeId 目录架构扩展（在本例中，此应用的应用 ID 为 ab603c56068041afb2f6832e2a17e237）。 SAML 令牌会将 Skype ID 公开为 `extension_skypeId`。

**UI 配置：**

1. 登录到 [Azure 门户](https://portal.azure.com)

1. 通过身份验证后，在页面右上角选择 Azure AD 租户。

1. 在左侧菜单中，选择“Azure Active Directory”。

1. 在“管理”部分下选择“应用注册” 。

1. 在列表中找到要为其配置可选声明的应用程序并选择它。

1. 在“管理”部分下，选择“令牌配置” 。

1. 选择“添加可选声明”，选择 ID 令牌类型，从声明列表中选择 upn，然后选择“添加”   。

1. 选择“添加可选声明”，选择“访问”令牌类型，从声明列表中选择 auth_time，然后选择“添加”   。

1. 在“令牌配置”概览屏幕中，选择 upn 旁边的铅笔图标，选择“外部身份验证”切换开关，然后选择“保存”  。

1. 选择“添加可选声明”，选择 SAML 令牌类型，从声明列表中选择 extn.skypeID（仅当创建了名为 skypeID 的 Azure AD 用户对象时才适用），然后选择“添加”   。

    [![演示如何使用 UI 配置可选声明](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**清单配置：**

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 通过身份验证后，在页面右上角选择 Azure AD 租户。
1. 从左侧菜单中选择“Azure Active Directory”。
1. 在列表中找到要为其配置可选声明的应用程序并选择它。
1. 在“管理”部分下，选择“清单”打开内联的清单编辑器 。
1. 可使用此编辑器直接编辑清单。 该清单遵循 [Application 实体](./reference-app-manifest.md)的架构，保存后会自动设置格式。 新元素将添加到 `OptionalClaims` 属性。

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. 更新完清单后，选择“保存”以保存清单。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 提供的标准声明。

- [ID 令牌](id-tokens.md)
- [访问令牌](access-tokens.md)
