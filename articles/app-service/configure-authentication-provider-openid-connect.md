---
title: " (预览配置 OpenID Connect 提供程序) "
description: 了解如何为应用服务或 Azure Functions 应用配置 OpenID Connect 提供程序作为标识提供者。
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: 1a4f956c15fae640c2a7978a14bb95328dc9aa71
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209291"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>将应用服务或 Azure Functions 应用配置为使用 OpenID Connect 提供程序登录 (预览版) 

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure App Service 或 Azure Functions 配置为使用符合[OpenID connect 规范](https://openid.net/connect/)的自定义身份验证提供程序。 OpenID Connect (OIDC) 是许多标识提供商 (Idp) 所使用的行业标准。 若要将应用配置为使用 adherent IDP，则无需了解规范的详细信息。

你可以将应用程序配置为使用一个或多个 OIDC 提供程序。 每个必须在配置中为每个指定唯一名称，并且只有一个可用作默认重定向目标。

> [!CAUTION]
> 启用 OpenID Connect 提供程序将禁用通过某些客户端（例如 Azure 门户、Azure CLI 和 Azure PowerShell）对应用程序的应用服务身份验证/授权功能的管理。 此功能依赖于新的 API 图面，在预览期间，尚不能在所有管理体验中考虑。

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>向标识提供者注册应用程序

你的提供程序将需要你向其注册应用程序的详细信息。 请参阅与该提供者相关的说明。 需要收集应用程序的**客户端 ID**和**客户端密码**。

> [!IMPORTANT]
> 应用程序密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
>

> [!NOTE]
> 某些提供程序可能需要额外的配置步骤，以及如何使用它们所提供的值。 例如，Apple 提供的私钥不是自身用作 OIDC 客户端机密，而是必须使用它来创建一个将被视为在应用配置中提供的机密的 JWT (请参阅[使用 Apple 文档登录](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)的 "创建客户端密码" 部分) 
>

使用所选的设置名称，将客户端机密添加为应用的[应用程序设置](./configure-common.md#configure-app-settings)。 稍后记下该名称。

此外，还需要提供程序的 OpenID Connect 元数据。 这通常通过[配置元数据文档](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)公开，该文档是提供商的颁发者 URL 作为后缀 `/.well-known/openid-configuration` 。 收集此配置 URL。

如果无法使用配置元数据文档，则需要单独收集以下值：

- 颁发者 URL (有时显示为 `issuer`) 
- [OAuth 2.0 授权终结点](https://tools.ietf.org/html/rfc6749#section-3.1) (有时显示为 `authorization_endpoint`) 
- [OAuth 2.0 令牌终结点](https://tools.ietf.org/html/rfc6749#section-3.2) (有时显示为 `token_endpoint`) 
- [OAuth 2.0 JSON Web 密钥集](https://tools.ietf.org/html/rfc8414#section-2)文档的 URL (有时显示为 `jwks_uri`) 

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>向应用程序添加提供程序信息

> [!NOTE]
> 所需配置采用新的 API 格式，目前仅受[基于文件的配置 (预览) ](.\app-service-authentication-how-to.md#config-file)支持。 你将需要使用此类文件执行以下步骤。

本部分将指导你更新配置，使之包括你的新 IDP。 下面是一个配置示例。

1. 在 `identityProviders` 对象中，添加 `openIdConnectProviders` 一个对象（如果不存在）。
1. 在 `openIdConnectProviders` 对象中，为新提供程序添加一个密钥。 这是用于在其余配置中引用提供程序的友好名称。 例如，如果希望要求通过此提供程序对所有请求进行身份验证，则应将设置 `globalValidation.unauthenticatedClientAction` 为 "RedirectToLoginPage" 并将 `globalValidation.unauthenticatedClientAction` 其设置为此相同的友好名称。
1. 使用对象 `registration` 中的对象和对象（可选）将对象分配给该密钥 `login` ：
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "loginScopes": [],
             "loginParameterNames": [],
       }
    }
    ```

1. 在注册对象中，将设置 `clientId` 为所收集的客户端 ID，将设置 `clientCredential.secretSettingName` 为存储客户端机密的应用程序设置的名称，并创建 `openIdConnectConfiguration` 对象：

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. 在 `openIdConnectConfiguration` 对象中，提供先前收集的 OpenID connect 元数据。 根据你收集的信息，有两个选项可供选择：

    - 将 `wellKnownOpenIdConfiguration` 属性设置为之前收集的配置元数据 URL。
    - 或者，设置按如下所述收集的四个单独值：
        - 设置 `issuer` 为颁发者 URL
        - 设置 `authorizationEndpoint` 为授权终结点
        - 设置 `tokenEndpoint` 为令牌终结点
        - 设置 `certificationUri` 为 JSON Web 密钥集文档的 URL

    这两个选项是互斥的。

设置此配置后，即可在应用中使用 OpenID Connect 提供程序进行身份验证。

示例配置可能如下所示 (使用 Apple 的登录作为示例，其中 APPLE_GENERATED_CLIENT_SECRET 设置指向根据[apple 文档](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)) 生成的 JWT：

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
                        "authorizationEndpoint": "https://appleid.apple.com/.well-known/openid-configuration"
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
