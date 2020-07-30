---
title: 在声明中使用 Azure AD 架构扩展属性
titleSuffix: Microsoft identity platform
description: 介绍如何使用目录架构扩展属性向令牌声明中的应用程序发送用户数据。
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
ms.openlocfilehash: 71dcad908884351950c89b590523ffa91d6d47f9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424753"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>在声明中使用目录架构扩展属性

目录架构扩展属性提供了一种方法，用于在用户对象和其他目录对象（例如组、租户详细信息、服务主体）上 Azure Active Directory 存储其他数据。  仅用户对象上的扩展属性可用于向应用程序发出声明。 本文介绍如何使用目录架构扩展属性向令牌声明中的应用程序发送用户数据。

> [!NOTE]
> Microsoft Graph 提供了两个其他扩展机制来自定义 Graph 对象。 这称为 Microsoft Graph 打开扩展和 Microsoft Graph 架构扩展。 有关详细信息，请参阅[Microsoft Graph 文档](/graph/extensibility-overview)。 使用这些功能的 Microsoft Graph 对象上存储的数据不可用作标记中声明的源。

目录架构扩展属性始终与租户中的应用程序相关联，并在其名称中由应用程序的*applicationId*引用。

目录架构扩展属性的标识符的格式为*Extension_xxxxxxxxx_AttributeName*。  其中， *xxxxxxxxx*是为其定义扩展的应用程序的*applicationId* 。

## <a name="registering-and-using-directory-schema-extensions"></a>注册和使用目录架构扩展
可以通过以下两种方式之一注册和填充目录架构扩展属性：

- 通过配置 AD Connect 来创建它们，并将数据从本地 AD 同步到它们。 请参阅[Azure AD Connect 同步目录扩展](/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions)。
- 使用 Microsoft Graph 注册、设置的值，以及从目录架构扩展属性中读取属性[目录架构扩展 |图形 API 概念](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)和/或 powershell +[通过 AzureAD PowerShell cmdlet 管理扩展属性](/powershell/azure/active-directory/using-extension-attributes-sample?view=azureadps-2.0)。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>发出声明，其中包含通过 AD Connect 创建的目录架构扩展属性中的数据
使用 AD Connect 创建和同步的目录架构扩展属性始终与 AD Connect 使用的应用程序 ID 相关联。 通过**在门户**UI 的 "企业应用程序" 配置中将其配置为声明，可将它们作为声明的源：将其配置为门户 UI 中的 "**企业应用程序**" 配置中的声明。  通过 AD Connect 创建的目录扩展属性在目录中，它将显示在 SAML SSO 声明配置 UI 中。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>发出声明，其中包含使用图形或 PowerShell 为应用程序创建的目录架构扩展属性中的数据
如果使用 Microsoft Graph 或 PowerShell （通过应用程序的初始设置或预配步骤）为应用程序注册目录架构扩展属性，则可以在 Azure Active Directory 中配置同一应用程序，以便在用户登录时从声明中的用户对象接收该属性中的数据。  可将应用程序配置为在使用[可选声明](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)在同一应用程序上注册的目录架构扩展中接收数据。  可以在应用程序清单中设置这些项。  这使多租户应用程序可以注册目录架构扩展属性以供自己使用。 将应用程序预配到租户后，相关联的目录架构扩展即可在该租户中的用户上进行设置和使用。  一旦将其配置为租户并获得同意，就可以使用它来存储和检索数据，并将其映射到 Azure AD 发出到应用程序的令牌中的声明。

可以为任何应用程序注册和填充目录架构扩展属性。

如果应用程序需要使用其他应用程序上注册的扩展属性中的数据发送声明，则必须使用[声明映射策略](active-directory-claims-mapping.md)将扩展属性映射到声明。  管理目录架构扩展属性的常见模式是创建一个应用程序，该应用程序是专门用于注册所需的所有架构扩展点的。  它不一定是实际的应用程序，而此技术意味着所有扩展在其名称中都具有相同的应用程序 ID。

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

其中， *xxxxxxx-xxxx ...* 是向其注册扩展的应用程序 ID。

> [!TIP]
> 在对象上设置目录扩展属性时，大小写一致性非常重要。  设置时，扩展属性名称不区分大小写，但当令牌服务从目录中读取时，它们区分大小写。  如果在名为 "LegacyId" 的用户对象和另一个名为 "LegacyId" 的用户对象上设置扩展属性，则在将该属性映射到使用名称 "LegacyId" 的声明时，将成功检索该数据，并将声明包含在第一个用户的令牌中，而不是第二个用户。
>
> 用于内置目录属性的声明架构中的 "Id" 参数为目录扩展属性的 "ExtensionID"。

## <a name="next-steps"></a>后续步骤
- 了解如何[将自定义或附加的声明添加到 SAML 2.0 和 JSON Web 令牌（JWT）令牌](active-directory-optional-claims.md)。 
- 了解如何[自定义特定应用的令牌中发出的声明](active-directory-claims-mapping.md)。