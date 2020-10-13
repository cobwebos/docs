---
title: 在声明中使用 Azure AD 架构扩展属性
titleSuffix: Microsoft identity platform
description: 介绍如何在令牌声明中使用目录架构扩展属性将用户数据发送到应用程序。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 8861e641f5ee6a10576425a7702ba02da297a0bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631267"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>在声明中使用目录架构扩展属性

目录架构扩展属性提供了一种方法来存储 Azure Active Directory 中用户对象和其他目录对象（例如组、租户详细信息、服务主体）上的其他数据。  仅用户对象上的扩展属性可用于向应用程序发出声明。 本文介绍如何在令牌声明中使用目录架构扩展属性将用户数据发送到应用程序。

> [!NOTE]
> Microsoft Graph 提供了两种可自定义 Graph 对象的其他扩展机制。 它们分别称为 Microsoft Graph 开启扩展和 Microsoft Graph 架构扩展。 请参阅 [Microsoft Graph 文档](/graph/extensibility-overview)以了解详细信息。 使用这些功能存储在 Microsoft Graph 对象上的数据不能用作令牌中声明的源。

目录架构扩展属性始终与租户中的应用程序相关联，并由应用程序的 applicationId 在其名称中引用。

目录架构扩展属性标识符的格式为 Extension_xxxxxxxxx_AttributeName。  其中 xxxxxxxxx 是为其定义扩展的应用程序的 applicationId 。

## <a name="registering-and-using-directory-schema-extensions"></a>注册和使用目录架构扩展
可通过以下两种方式之一注册和填充目录架构扩展属性：

- 通过配置 AD Connect 来创建它们，并将数据从本地 AD 同步其中。 请参阅 [Azure AD Connect 同步目录扩展](../hybrid/how-to-connect-sync-feature-directory-extensions.md)。
- 通过使用 Microsoft Graph 注册，设置的值，并从 [架构扩展](/graph/extensibility-overview)中进行读取。 还提供了[PowerShell cmdlet](/powershell/azure/active-directory/using-extension-attributes-sample) 。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>发出包含使用 AD Connect 创建的目录架构扩展属性中数据的声明
使用 AD Connect 创建和同步的目录架构扩展属性始终与 AD Connect 使用的应用程序 ID 相关联。 可通过两种方式将其用作声明的源：对于在“企业应用程序”下方使用“库”或“非库”应用程序配置体验注册的 SAML 应用程序，在门户 UI 中“企业应用程序”配置中将其配置为声明；对于通过应用程序注册体验注册的应用程序，通过声明映射策略进行配置。   通过 AD Connect 创建的目录扩展属性位于目录中之后，它将显示在 SAML SSO 声明配置 UI 中。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>使用 Graph 或 PowerShell 发出包含为应用程序所创建目录架构扩展属性中数据的声明
如果使用 Microsoft Graph 或 PowerShell 为应用程序注册目录架构扩展属性（例如通过应用程序初始设置或预配步骤），则可以在 Azure Active Directory 中将同一应用程序配置为在用户登录时接收来自声明中的用户对象的该属性中的数据。  可将应用程序配置为在目录架构扩展中接收数据，该扩展使用[可选声明](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)在同一应用程序上进行注册。  可以在应用程序清单中设置它们。  这使多租户应用程序可以注册目录架构扩展属性以供自己使用。 将应用程序预配到租户中后，相关联的目录架构扩展可以在该租户中的用户上进行设置并可以进行使用。  一旦将其配置为租户并获得同意，就可以使用它通过图形存储和检索数据，并映射到 Microsoft 标识平台向应用程序发出的令牌中的声明。

可以为任何应用程序注册和填充目录架构扩展属性。

如果应用程序需要发送声明，该声明具有来自在不同的应用程序上注册的扩展属性的数据，则必须使用[声明映射策略](active-directory-claims-mapping.md)将扩展属性映射到声明。  管理目录架构扩展属性的一种常见模式是创建一个应用程序，该应用程序专门用作所需的所有架构扩展的注册点。  它不一定是实际的应用程序，且此技术意味着所有扩展在其名称中都具有相同的应用程序 ID。

例如，以下声明映射策略用于从 OAuth/OIDC 令牌中的目录架构扩展属性发出单个声明：

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

其中 xxxxxxx 是向其注册扩展的应用程序 ID。

> [!TIP]
> 在对象上设置目录扩展属性时，大小写一致性非常重要。  扩展属性名称在设置时不区分大小写，但在令牌服务从目录读取时区分大小写。  如果在名称为“LegacyId”的用户对象和另一个名称为“legacyid”的用户对象上设置了扩展属性，则当使用名称“LegacyId”将属性映射到声明时，将成功检索数据，且声明将包含在第一位用户的令牌中，而不是第二位用户的令牌中。
>
> 用于内置目录属性的声明架构中的“Id”参数是目录扩展属性的“ExtensionID”。

## <a name="next-steps"></a>后续步骤
- 了解如何[向 SAML 2.0 和 JSON Web 令牌 (JWT) 令牌添加自定义声明或额外声明](active-directory-optional-claims.md)。
- 了解如何[为特定应用自定义在令牌中发出的声明](active-directory-claims-mapping.md)。