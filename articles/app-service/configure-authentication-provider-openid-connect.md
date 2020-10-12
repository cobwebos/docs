---
title: 配置 OpenID Connect 提供程序（预览版）
description: 了解如何将 OpenID Connect 提供程序配置为应用服务或 Azure Functions 应用的标识提供者。
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983876"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>将应用服务或 Azure Functions 应用配置为使用 OpenID Connect 提供程序（预览版）进行登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍了如何将 Azure 应用服务或 Azure Functions 配置为使用符合 [OpenID Connect 规范](https://openid.net/connect/)的自定义身份验证提供程序。 OpenID Connect (OIDC) 是许多标识提供者 (IDP) 使用的行业标准。 无需了解规范的详细信息便可将应用配置为使用符合规范的 IDP。

你可以将应用配置为使用一个或多个 OIDC 提供者。 必须在配置中为每个提供者指定唯一名称，并且只有一个可用作默认的重定向目标。

> [!CAUTION]
> 启用 OpenID Connect 提供者会禁止通过某些客户端（例如 Azure 门户、Azure CLI 和 Azure PowerShell）管理你的应用程序的应用服务身份验证/授权功能。 此功能依赖于一个新的 API 图面，该图面目前为预览版，并未应用于所有管理体验中。

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>向以下标识提供者注册你的应用程序

提供者会要求你向其注册应用程序的详细信息。 请参阅与该提供者相关的说明。 你需要收集应用程序的**客户端 ID** 和**客户端机密**。

> [!IMPORTANT]
> 应用程序密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
>

> [!NOTE]
> 某些提供者可能要求你执行额外的配置步骤，并说明了如何使用其提供的值。 例如，Apple 提供了一个私钥，该私钥本身并不用作 OIDC 客户端机密，你必须使用它来制作一个 JWT，该 JWT 被视为你在应用配置中提供的机密（请参阅[使用 Apple ID 登录](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)文档中的“创建客户端机密”部分）
>

使用你选择的设置名称将客户端机密添加为应用的[应用程序设置](./configure-common.md#configure-app-settings)。 记下此名称备用。

此外，你还需要提供者的 OpenID Connect 元数据。 这通常是通过一个[配置元数据文档](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)（提供者的颁发者 URL，以 `/.well-known/openid-configuration` 为后缀）公开的。 收集此配置 URL。

如果无法使用配置元数据文档，则需要单独收集以下值：

- 颁发者 URL（有时显示为 `issuer`）
- [OAuth 2.0 授权终结点](https://tools.ietf.org/html/rfc6749#section-3.1)（有时显示为 `authorization_endpoint`）
- [OAuth 2.0 令牌终结点](https://tools.ietf.org/html/rfc6749#section-3.2)（有时显示为 `token_endpoint`）
- [OAuth 2.0 JSON Web 密钥集](https://tools.ietf.org/html/rfc8414#section-2)文档的 URL（有时显示为 `jwks_uri`）

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>向应用程序添加提供者信息

> [!NOTE]
> 所需配置采用新的 API 格式，目前只有[基于文件的配置（预览版）](.\app-service-authentication-how-to.md#config-file)支持此格式。 你需要使用这样的文件执行以下步骤。

本部分将指导你更新配置，使之包括新的 IDP。 后面提供了配置示例。

1. 在 `identityProviders` 对象内，添加一个 `openIdConnectProviders` 对象（如果它尚不存在）。
1. 在 `openIdConnectProviders` 对象内，为新的提供者添加密钥。 这是用于在配置的其余部分中引用提供者的友好名称。 例如，如果你想要求所有请求都通过此提供者进行身份验证，则可将 `globalValidation.unauthenticatedClientAction` 设置为“RedirectToLoginPage”，并将 `redirectToProvider` 设置为此易记名称。
1. 向该密钥分配一个对象，在其中包含 `registration` 对象，还可以包含 `login` 对象：
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. 在 registration 对象内，将 `clientId` 设置为你收集的客户端 ID，将 `clientCredential.secretSettingName` 设置为你存储客户端机密的应用程序设置的名称，并创建一个 `openIdConnectConfiguration` 对象：

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. 在 `openIdConnectConfiguration` 对象内，提供你之前收集的 OpenID Connect 元数据。 根据你收集的信息，有两个选项可供选择：

    - 将 `wellKnownOpenIdConfiguration` 属性设置为你之前收集的配置元数据 URL。
    - 另外，还可以分别设置四个单独的值，如下所述：
        - 将 `issuer` 设置为颁发者 URL
        - 将 `authorizationEndpoint` 设置为授权终结点
        - 将 `tokenEndpoint` 设置为令牌终结点
        - 将 `certificationUri` 设置为 JSON Web 密钥集文档的 URL

    这两个选项是互斥的。

设置此配置后，即可在应用中使用 OpenID Connect 提供者进行身份验证。

示例配置可能如下所示（以“使用 Apple ID 登录”为例，根据 [Apple 文档](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)，其中的 APPLE_GENERATED_CLIENT_SECRET 设置指向生成的一个 JWT）：

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
