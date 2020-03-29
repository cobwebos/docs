---
title: Azure AD B2C （MSAL 安卓） |蔚蓝
titleSuffix: Microsoft identity platform
description: 了解将 Azure AD B2C 与适用于 Android 的 Microsoft 身份验证库 (MSAL.Android) 配合使用时的具体注意事项。
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696515"
---
# <a name="use-msal-for-android-with-b2c"></a>将适用于 Android 的 MSAL 与 B2C 配合使用

借助 Microsoft 身份验证库 (MSAL)，应用程序开发人员可以使用 [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) 通过社交和本地标识对用户进行身份验证。 Azure AD B2C 是一个标识管理服务。 使用该服务可以在客户使用你的应用程序时，自定义和控制他们的注册和登录方式以及管理其个人资料。

## <a name="configure-known-authorities-and-redirect-uri"></a>配置已知的颁发机构和重定向 URI

在适用于 Android 的 MSAL 中，B2C 策略（用户旅程）配置为单独的颁发机构。

假设某个 B2C 应用程序有两个策略：
- 注册/登录
    * 名为 `B2C_1_SISOPolicy`
- 编辑个人资料
    * 名为 `B2C_1_EditProfile`

应用的配置文件将声明两个 `authorities`。 对每个策略各声明一个。 每个颁发机构的 `type` 属性为 `B2C`。

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

`redirect_uri` 必须在应用配置中注册，此外还必须在 `AndroidManifest.xml` 中注册，以便在运行[授权代码授予流](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)期间支持重定向。

## <a name="initialize-ipublicclientapplication"></a>初始化 IPublicClientApplication

`IPublicClientApplication` 由某个工厂方法构造，使用它可以异步方式分析应用程序配置。

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

若要使用 MSAL 以交互方式获取令牌，请生成一个 `AcquireTokenParameters` 实例并将其提供给 `acquireToken` 方法。 以下令牌请求使用 `default` 颁发机构。

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

## <a name="silently-renew-a-token"></a>以静默方式续订令牌

若要使用 MSAL 以静默方式获取令牌，请生成一个 `AcquireTokenSilentParameters` 实例并将其提供给 `acquireTokenSilentAsync` 方法。 与 `acquireToken` 方法不同，必须指定 `authority` 才能以静默方式获取令牌。

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

由于 B2C 中的策略以单独的颁发机构表示，因此，可以通过在构造 `acquireToken` 或 `acquireTokenSilent` 参数时指定 `fromAuthority` 子句，来调用除默认策略以外的策略。  例如：

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

本地帐户注册或登录用户流显示“忘记了密码?”**** 链接。 单击此链接不会自动触发密码重置用户流。

而是会将错误代码 `AADB2C90118` 返回给应用。 应用应该通过运行一个可重置密码的特定用户流来处理此错误代码。

若要捕获密码重置错误代码，可以在 `AuthenticationCallback` 中使用以下实现：

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

成功获取令牌后会生成 `IAuthenticationResult` 对象。 该对象包含访问令牌、用户声明和元数据。

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

### <a name="get-the-authorized-account"></a>获取已获授权的帐户

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

IdToken 中返回的声明由安全令牌服务 (STS) 而不是 MSAL 填充。 根据所用的标识提供者 (IdP)，可能缺少某些声明。 某些 IdP 目前不提供 `preferred_username` 声明。 由于 MSAL 将此声明用于缓存，因此，已使用一个占位符值 `MISSING FROM THE TOKEN RESPONSE` 来代替此声明。 有关 B2C IdToken 声明的详细信息，请参阅 [Azure Active Directory B2C 中的令牌概述](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)

## <a name="managing-accounts-and-policies"></a>管理帐户和策略

B2C 将每个策略视为单独的颁发机构。 因此，从每个策略返回的访问令牌、刷新令牌和 ID 令牌不可换用。 这意味着，每个策略将返回单独的 `IAccount` 对象，该对象的令牌不可用于调用其他策略。

每个策略将 `IAccount` 添加到每个用户的缓存。 如果用户登录到应用程序并调用两个策略，他们会收到两个 `IAccount`。 若要从缓存中删除此用户，必须对每个策略调用 `removeAccount()`。

使用 `acquireTokenSilent` 续订策略的令牌时，请将以前调用策略后返回的相同 `IAccount` 提供给 `AcquireTokenSilentParameters`。 提供另一个策略返回的帐户会导致错误。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Active Directory B2C (Azure AD B2C)，请参阅[什么是 Azure Active Directory B2C？](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
