---
title: Azure AD B2C （适用于 Android 的 Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解将 Azure AD B2C 与适用于 Android 的 Microsoft 身份验证库（MSAL）一起使用时的特定注意事项。Android
services: active-directory
documentationcenter: dev-center-name
author: brianmel
manager: omkrishn
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5061f1ab341e5872dfa82c9f5c5b133ae40bdf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803237"
---
# <a name="use-msal-for-android-with-b2c"></a>使用适用于 Android 的 MSAL 和 B2C

借助 Microsoft 身份验证库 (MSAL)，应用程序开发人员可以使用 [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) 通过社交和本地标识对用户进行身份验证。 Azure AD B2C 是一个标识管理服务。 使用它可以自定义和控制客户使用应用程序时，如何注册、登录和管理他们的配置文件。

## <a name="configure-known-authorities-and-redirect-uri"></a>配置已知的颁发机构和重定向 URI

在 Android MSAL 中，B2C 策略（user 旅程）配置为单独的颁发机构。

给定一个具有两个策略的 B2C 应用程序：
- 注册/登录
    * 调用 `B2C_1_SISOPolicy`
- 编辑配置文件
    * 调用 `B2C_1_EditProfile`

应用的配置文件将声明两个 `authorities`。 每个策略都有一个。 `B2C`每个颁发机构的 `type` 属性。

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

`redirect_uri` 必须在应用程序配置中注册，并且还必须在 `AndroidManifest.xml` 中，以便在[授权代码授予流](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)期间支持重定向。

## <a name="initialize-ipublicclientapplication"></a>初始化 IPublicClientApplication

`IPublicClientApplication` 由工厂方法构造，以允许以异步方式分析应用程序配置。

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>以交互方式获取令牌

若要以交互方式使用 MSAL 获取令牌，请生成一个 `AcquireTokenParameters` 实例，并将其提供给 `acquireToken` 方法。 以下令牌请求使用 `default` 颁发机构。

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>以无提示方式续订令牌

若要以无提示方式使用 MSAL 获取令牌，请生成一个 `AcquireTokenSilentParameters` 实例，并将其提供给 `acquireTokenSilentAsync` 方法。 与 `acquireToken` 方法不同，必须指定 `authority` 以无提示方式获取令牌。

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>指定策略

因为 B2C 中的策略表示为单独的颁发机构，所以，调用默认的策略时，会通过在构造 `acquireToken` 或 `acquireTokenSilent` 参数时指定 `fromAuthority` 子句来实现。  例如：

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>处理密码更改策略

本地帐户注册或登录用户流显示 "**忘记了密码？** " 链接。 单击此链接不会自动触发密码重置用户流。

相反，错误代码 `AADB2C90118` 返回到你的应用程序。 应用应通过运行重置密码的特定用户流来处理此错误代码。

若要捕获密码重置错误代码，可以在 `AuthenticationCallback`中使用以下实现：

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>使用 IAuthenticationResult

成功的令牌获取会导致 `IAuthenticationResult` 的对象。 它包含访问令牌、用户声明和元数据。

### <a name="get-the-access-token-and-related-properties"></a>获取访问令牌和相关属性

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>获取授权帐户

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken 声明

IdToken 中返回的声明由安全令牌服务（STS）（而不是 MSAL）填充。 根据所使用的标识提供者（IdP），某些声明可能不存在。 有些 Idp 当前不提供 `preferred_username` 声明。 因为 MSAL 使用此声明来缓存，所以占位符值 `MISSING FROM THE TOKEN RESPONSE`在其位置使用。 有关 B2C IdToken 声明的详细信息，请参阅[Azure Active Directory B2C 中标记的概述](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)。

## <a name="managing-accounts-and-policies"></a>管理帐户和策略

B2C 将每个策略视为单独的颁发机构。 因此，从每个策略返回的访问令牌、刷新令牌和 ID 令牌不能互换。 这意味着，每个策略都返回一个单独的 `IAccount` 对象，其令牌不能用于调用其他策略。

每个策略都会向每个用户的缓存中添加一个 `IAccount`。 如果用户登录到应用程序并调用两个策略，则它们将具有两个 `IAccount`。 若要从缓存中删除此用户，你必须为每个策略调用 `removeAccount()`。

使用 `acquireTokenSilent`续订策略的令牌时，请提供从以前的策略调用返回的与 `AcquireTokenSilentParameters`相同的 `IAccount`。 提供另一个策略返回的帐户会导致错误。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Active Directory B2C Azure Active Directory B2C （Azure AD B2C） [？](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
