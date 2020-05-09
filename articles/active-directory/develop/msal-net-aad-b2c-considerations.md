---
title: Azure AD B2C 和 MSAL.NET
titleSuffix: Microsoft identity platform
description: 将 Azure AD B2C 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时的注意事项。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3aac63369dffa5b8ba0b9e55b5063ad8136c95cf
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883220"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 通过社交标识将用户登录

可以在 [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c) 中使用 MSAL.NET 通过社交标识将用户登录。 Azure AD B2C 是围绕策略这一概念构建的。 在 MSAL.NET 中，指定策略相当于提供颁发机构。

- 实例化公用客户端应用程序时，需要将策略指定为颁发机构的一部分。
- 如果要应用策略，请调用接受`AcquireTokenInteractive` `authority`参数的的替代。

本文适用于 MSAL.NET 1.x。 对于 MSAL.NET 2.x，请参阅 GitHub 上的 MSAL.NET Wiki 中的 MSAL 2.x 中[Azure AD B2C 详细信息](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租户和策略的颁发机构

Azure AD B2C 的授权格式为：`https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname`-Azure AD B2C 租户和主机的名称。 例如， *contosob2c.b2clogin.com*。
- `tenant`-域名或 Azure AD B2C 租户的目录（租户） ID。 例如， *contosob2c.onmicrosoft.com*或 GUID。
- `policyName`-要应用的用户流或自定义策略的名称。 例如，诸如*b2c_1_susi*的注册/登录策略。

有关 Azure AD B2C 机构的详细信息，请参阅[将重定向 Url 设置为 b2clogin.com](../../active-directory-b2c/b2clogin.md)。

## <a name="instantiating-the-application"></a>实例化应用程序

当你创建应用程序`WithB2CAuthority()`对象时，通过调用来提供权限：

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>获取用于应用策略的令牌

若要在公共客户端应用程序中为受 Azure AD B2C 保护的 API 获取令牌，需要将替代与颁发机构配合使用：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

在前面的代码片段中：

- `policy`一个字符串，其中包含 Azure AD B2C 用户流或自定义策略的名称（例如`PolicySignUpSignIn`）。
- `ParentActivityOrWindow`对于 Android （活动）是必需的，并且对于支持父 UI （如 Microsoft Windows 上的 windows 和 iOS 中的 UIViewController）的其他平台是可选的。 有关 UI 对话框的详细信息，请参阅 MSAL Wiki 上的[WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) 。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`：用于在帐户中查找给定策略的方法。 例如：

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
      foreach (var account in accounts)
      {
          string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
          if (userIdentifier.EndsWith(policy.ToLower()))
              return account;
      }
      return null;
  }
  ```

应用用户流或自定义策略（例如，允许用户编辑其配置文件或重置其密码）当前是通过调用`AcquireTokenInteractive`来完成的。 对于这两个策略，不使用返回的令牌/身份验证结果。

## <a name="profile-edit-policies"></a>配置文件编辑策略

若要使用户能够使用社交标识登录，并编辑其配置文件，请应用 Azure AD B2C 编辑配置文件策略。

为此，请`AcquireTokenInteractive`使用该策略的权限调用。 由于用户已登录并具有活动 cookie 会话，请使用`Prompt.NoPrompt`阻止显示 "帐户选择" 对话框。

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
    try
    {
        var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                            .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                            .WithPrompt(Prompt.NoPrompt),
                            .WithB2CAuthority(App.AuthorityEditProfile)
                            .ExecuteAsync();
        DisplayBasicTokenInfo(authResult);
    }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>资源所有者密码凭据（ROPC）

有关 ROPC 流的详细信息，请参阅[利用资源所有者密码凭据授予登录](v2-oauth-ropc.md)。

**不建议使用**ROPC 流，因为在应用程序中请求其密码的用户不安全。 有关此问题的详细信息，请参阅[不断增长的密码问题的解决方案是什么？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。

通过在 ROPC flow 中使用用户名/密码，你可以牺牲几个问题：

- 新式标识的核心原则：可以 fished 或重播密码，因为共享机密可以被截取。 根据定义，ROPC 与无密码流不兼容。
- 需要执行 MFA 的用户将无法登录（因为没有交互）。
- 用户将无法使用单一登录（SSO）。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 配置 ROPC 流

在 Azure AD B2C 租户中，创建一个新的用户流，然后选择 "**使用 ROPC 登录**" 以为用户流启用 ROPC。 有关详细信息，请参阅[配置资源所有者密码凭据流](/azure/active-directory-b2c/configure-ropc)。

`IPublicClientApplication`包含`AcquireTokenByUsernamePassword`方法：

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

此`AcquireTokenByUsernamePassword`方法采用以下参数：

- 要为其获取访问令牌的*范围*。
- 一个用户名。 
- 用户的安全字符串密码。 

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流的限制

ROPC 流**仅适用于本地帐户**，其中用户已使用电子邮件地址或用户名注册到 Azure AD B2C。 与 Azure AD B2C （Facebook、Google 等）支持的外部标识提供程序进行联合时，此流不起作用。

## <a name="google-auth-and-embedded-webview"></a>Google 身份验证和嵌入式 web 视图

如果你使用 Google 作为标识提供者，则建议你使用系统浏览器，因为 Google 不允许[从 embedded webview 进行身份验证](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前， `login.microsoftonline.com`是使用 Google 的受信任的颁发机构，适用于嵌入的 web 视图。 但是， `b2clogin.com`不是 Google 的受信任的颁发机构，因此用户将无法进行身份验证。

如果发生更改，我们将提供对此[问题](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)的更新。

## <a name="token-caching-in-msalnet"></a>MSAL.NET 中的令牌缓存

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知问题

MSAL.NET 支持[令牌缓存](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)。 令牌缓存密钥基于标识提供者（IdP）返回的声明。

目前，MSAL.NET 需要两个声明来生成令牌缓存密钥：

- `tid`（Azure AD 租户 ID）
- `preferred_username`

在 Azure AD B2C 情况下，这两个声明可能都丢失，因为并非所有社交标识提供者（Facebook、Google 和其他）都在其返回到 Azure AD B2C 的令牌中返回它们。

此类情况的症状是，当你访问`Missing from the token response` Azure AD B2C 颁发的令牌`preferred_username`中的声明值时，MSAL.NET 返回。 MSAL 使用的`Missing from the token response`值`preferred_username`来维护库之间的缓存交叉兼容。

### <a name="workarounds"></a>解决方法

#### <a name="mitigation-for-missing-tenant-id"></a>缺少的租户 ID 的缓解措施

建议的解决方法是使用前面所述[的策略的缓存](#acquire-a-token-to-apply-a-policy)。

或者，如果使用的是`tid` Azure AD B2C 中的[自定义策略](../../active-directory-b2c/custom-policy-get-started.md)，则可以使用声明。 自定义策略可以使用[声明转换](/azure/active-directory-b2c/claims-transformation-technical-profile)将其他声明返回到应用程序。

#### <a name="mitigation-for-missing-from-the-token-response"></a>“在令牌响应中缺失”的缓解措施

一种选择是使用`name`声明，而不`preferred_username`是。 若要包含`name` Azure AD B2C 颁发的 ID 令牌中的声明，请在配置用户流时选择 "**显示名称**"。

有关指定用户流返回的声明的详细信息，请参阅[教程：在 Azure AD B2C 中创建用户流](../../active-directory-b2c/tutorial-create-user-flows.md)。

## <a name="next-steps"></a>后续步骤

以下示例提供了有关使用 MSAL.NET 以交互方式获取 Azure AD B2C 应用程序的令牌的更多详细信息。

| 示例 | 平台 | 说明|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | 一个 Xamarin 窗体应用，它使用 MSAL.NET 通过 Azure AD B2C 对用户进行身份验证，然后使用返回的令牌来访问 web API。|
