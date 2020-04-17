---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解将 Azure AD B2C 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的具体注意事项。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d31cf3a4e024dc59b865d096cbd0829d50f61a1a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533949"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 通过社交标识将用户登录

可以在 [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c) 中使用 MSAL.NET 通过社交标识将用户登录。 Azure AD B2C 是围绕策略这一概念构建的。 在 MSAL.NET 中，指定策略相当于提供颁发机构。

- 实例化公共客户端应用程序时，需要在颁发机构中指定策略。
- 想要应用策略时，需要调用包含 `authority` 参数的 `AcquireTokenInteractive` 的重写。

本页面的内容适用于 MSAL 3.x。 如果你对 MSAL 2.x 感兴趣，请参阅 [MSAL 2.x 中的 Azure AD B2C 细节](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租户和策略的颁发机构

要使用的颁发机构是 `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`，其中：

- `azureADB2CHostname` 是 Azure AD B2C 租户的名称加上主机（例如 `{your-tenant-name}.b2clogin.com`），
- `tenant` 是 Azure AD B2C 租户的完整名称（例如，`{your-tenant-name}.onmicrosoft.com`）或租户的 GUID，
- `policyName` 要应用的策略或用户流的名称（例如，“b2c_1_susi”用于注册/登录）。

有关 Azure AD B2C 机构的详细信息，请参阅此[文档](/azure/active-directory-b2c/b2clogin)。

## <a name="instantiating-the-application"></a>实例化应用程序

生成应用程序时，需要提供颁发机构。

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

在公共客户端应用程序中获取受 Azure AD B2C 保护的 API 的令牌需要使用颁发机构的重写：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

替换为：

- `policy`：前面的字符串之一（例如 `PolicySignUpSignIn`）。
- `ParentActivityOrWindow` 对于 Android（活动）是必需的，对于支持父 UI 的其他平台（例如 Windows 中的窗口和 iOS 中的 UIViewController）是可选的。 在[此处的 UI 对话框](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)中查看更多信息。
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

目前通过调用 `AcquireTokenInteractive` 来应用策略或用户流（例如，让最终用户编辑其配置文件或重置其密码）。 对于这两个策略，不使用返回的令牌/身份验证结果。

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile 和 ResetPassword 策略的特殊情况

若要提供让最终用户使用社交标识登录，然后编辑其个人资料的体验，需要应用 Azure AD B2C 编辑配置文件策略。 为此，可以使用该策略的特定权限调用 `AcquireTokenInteractive`，并将 Prompt 设置为 `Prompt.NoPrompt` 以避免显示帐户选择对话框（因为用户已登录并拥有活动的 Cookie 会话）。

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
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Azure AD B2C 中的资源所有者密码凭据 (ROPC)
有关 ROPC 流的更多详细信息，请参阅[此文档](v2-oauth-ropc.md)。

**不建议**使用此流，因为要求用户提供其密码的应用程序是不安全的。 有关此问题的详细信息，请参阅[此文](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。

使用用户名/密码意味着会丧失许多功能：
- 新式标识的核心原则：密码被窃取、重放。 我们的观点是共享机密可能会被截获。 此方法与无密码登录是不兼容的。
- 需要执行 MFA 的用户将无法登录（因为没有交互）。
- 用户无法执行单一登录。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 配置 ROPC 流
在 Azure AD B2C 租户中创建一个新的用户流，然后选择“使用 ROPC 登录”。**** 这会为租户启用 ROPC 策略。 有关更多详细信息，请参阅[配置资源所有者密码凭据流](/azure/active-directory-b2c/configure-ropc)。

`IPublicClientApplication` 包含一个方法：
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

此方法采用以下参数：
- 要请求其访问令牌的范围。**
- 一个用户名。**
- 用户的安全字符串密码。**

请记得使用包含 ROPC 策略的颁发机构。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流的限制
 - ROPC 流**仅适用于本地帐户**（使用电子邮件或用户名注册到 Azure AD B2C 的帐户）。 如果与 Azure AD B2C 支持的任何标识提供程序（Facebook、Google 等）联合，则此流不起作用。

## <a name="google-auth-and-embedded-webview"></a>谷歌Auth和嵌入式网页视图

如果您是使用 Google 作为身份提供商的 Azure AD B2C 开发人员，我们会重新命令您使用系统浏览器，因为 Google 不允许[从嵌入式网页进行身份验证](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前，`login.microsoftonline.com`谷歌是一个值得信赖的机构。 使用此权限将处理嵌入式 Webview。 但是，`b2clogin.com`使用不是 Google 的可信权限，因此用户将无法进行身份验证。

如果情况发生变化，我们将提供[此问题](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)的更新。

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>在 MSAL.Net 中使用 Azure AD B2C 进行缓存

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知问题

MSAL.Net 支持[令牌缓存](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)。 缓存令牌密钥基于标识提供者返回的声明。 目前，MSAL.Net 需要使用两个声明来生成令牌缓存密钥：
- `tid`：Azure AD 租户 ID；
- `preferred_username`

许多 Azure AD B2C 方案中缺少这两个声明。

判断客户是否受到影响的依据是，在尝试显示用户名字段时，值是否显示为“在令牌响应中缺失”？ 如果是，原因是 Azure AD B2C 不会在 IdToken 中返回 preferred_username 的值，因为社交帐户和外部标识提供者 (IdP) 存在限制。 Azure AD 返回preferred_username的值，因为它知道用户是谁，但对于 Azure AD B2C，因为用户可以使用本地帐户、Facebook、Google、GitHub 等登录，因此 Azure AD B2C 没有用于preferred_username的一致值。 为了阻止 MSAL 实施与 ADAL 的缓存兼容性，我们决定在处理 Azure AD B2C 帐户的过程中，当 IdToken 未返回 preferred_username 的任何值时，在我们一端使用“在令牌响应中缺失”。 MSAL 必须返回 preferred_username 的值才能保持库间的缓存兼容性。

### <a name="workarounds"></a>解决方法

#### <a name="mitigation-for-the-missing-tenant-id"></a>缺少租户 ID 的缓解措施

建议的解决方法是使用[按策略缓存](#acquire-a-token-to-apply-a-policy)

或者，如果您使用的是`tid`[B2C 自定义策略](https://aka.ms/ief)，则可以使用 声明，因为它提供了向应用程序返回其他声明的功能。 详细了解[声明转换](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>“在令牌响应中缺失”的缓解措施
一种做法是使用“name”声明作为首选用户名。 [B2C 文档](../../active-directory-b2c/user-flow-overview.md)中提到了该过程 ->“在‘返回声明’列中，选择需要在成功获得配置文件编辑体验后发回到应用程序的授权令牌中返回的声明。 例如，选择‘显示名称’、‘邮政编码’。”

## <a name="next-steps"></a>后续步骤

以下示例提供了有关使用 MSAL.NET 以交互方式获取 Azure AD B2C 应用程序的令牌的更多详细信息。

| 示例 | 平台 | 说明|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | 一个简单的 Xamarin 窗体应用，演示如何使用MSAL.NET通过 Azure AD B2C 对用户进行身份验证，并使用生成的令牌访问 Web API。|
