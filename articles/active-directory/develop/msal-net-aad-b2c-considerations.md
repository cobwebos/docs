---
title: Azure AD B2C （适用于 .NET 的 Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解将 Azure AD B2C 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时的特定注意事项。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a30f792a74ffc3aa983d84d902fa736a3f9b015
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802952"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 登录具有社交标识的用户

可以通过使用[Azure Active Directory B2C （Azure AD B2C）](https://aka.ms/aadb2c)，使用 MSAL.NET 通过社交标识登录用户。 Azure AD B2C 是围绕策略概念构建的。 在 MSAL.NET 中，指定策略会转换为提供机构。

- 实例化公用客户端应用程序时，需要在 "颁发机构" 中指定策略。
- 若要应用策略，需要调用包含 `authority` 参数的 `AcquireTokenInteractive` 的重写。

此页面适用于 MSAL 1.x。 如果你对 MSAL 2.x 感兴趣，请参阅 MSAL 2.x[中 Azure AD B2C 的详细信息](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租户和策略的颁发机构

要使用的授权 `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` 位置：

- `tenant` 是 Azure AD B2C 租户的名称， 
- `policyName` 要应用的策略的名称（例如，"b2c_1_susi" 用于登录/注册）。

Azure AD B2C 的当前指导原则是使用 `b2clogin.com` 作为颁发机构。 例如，`$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"` 。 有关详细信息，请参阅此[文档](/azure/active-directory-b2c/b2clogin)。

## <a name="instantiating-the-application"></a>实例化应用程序

构建应用程序时，需要提供该颁发机构。

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>获取令牌以应用策略

为公用客户端应用程序中的 Azure AD B2C 受保护的 API 获取令牌需要将替代与颁发机构配合使用：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

替换为：

- `policy` 是前面的字符串之一（如 `PolicySignUpSignIn`）。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` 是一种查找给定策略的帐户的方法。 例如：

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

应用策略（例如，允许最终用户编辑其配置文件或重置其密码）当前是通过调用 `AcquireTokenInteractive`来完成的。 对于这两个策略，不使用返回的令牌/身份验证结果。

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile 和 ResetPassword 策略的特殊情况

当你想要提供最终用户使用社交标识登录的体验，然后编辑他们的个人资料，你想要应用 Azure AD B2C EditProfile 策略。 执行此操作的方法是：调用具有该策略的特定权限的 `AcquireTokenInteractive`，并将提示设置为 "`Prompt.NoPrompt` 以避免显示帐户选择对话框（因为用户已登录）

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>资源所有者密码凭据（ROPC）与 Azure AD B2C
有关 ROPC 流的更多详细信息，请参阅此[文档](v2-oauth-ropc.md)。

**不建议使用**此流，因为你的应用程序要求用户输入其密码是不安全的。 有关此问题的详细信息，请参阅[此文](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

通过使用用户名/密码，你将获得多项内容：
- 新式标识的核心租户： password 获取 fished，重播。 因为我们有一个可以被截取的共享机密的概念。 这与无密码不兼容。
- 需要进行 MFA 的用户将无法登录（因为没有交互）。
- 用户无法进行单一登录。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 中配置 ROPC 流
在 Azure AD B2C 租户中，创建一个新的用户流，然后选择 **"使用 ROPC 登录"** 。 这将为租户启用 ROPC 策略。 有关更多详细信息，请参阅[配置资源所有者密码凭据流](/azure/active-directory-b2c/configure-ropc)。

`IPublicClientApplication` 包含方法：
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

此方法采用作为参数：
- 要为其请求访问令牌的*范围*。
- *用户名*。
- 用户的 SecureString*密码*。

请记得使用包含 ROPC 策略的授权机构。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流的限制
 - ROPC flow**仅适用于本地帐户**（使用电子邮件或用户名注册到 Azure AD B2C）。 如果与 Azure AD B2C （Facebook、Google 等）支持的任何标识提供程序进行联合，则此流不起作用。
 - 当前，从 MSAL 实现 ROPC 流时， **Azure AD B2C 未返回任何 id_token** 。 这意味着无法创建帐户对象，因此在缓存中，不会有帐户，也不会有用户。 在此方案中，AcquireTokenSilent 流将不起作用。 不过，ROPC 不显示 UI，因此不会影响用户体验。

## <a name="google-auth-and-embedded-webview"></a>Google 身份验证和嵌入式 Web 视图

如果你是使用 Google 作为标识提供者的 Azure AD B2C 开发人员，我们建议你使用系统浏览器，因为 Google 不允许[从 embedded webview 进行身份验证](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前，`login.microsoftonline.com` 是使用 Google 的受信任的颁发机构。 使用此权限将适用于嵌入的 web 视图。 但是，使用 `b2clogin.com` 不是 Google 的受信任的颁发机构，因此用户将无法进行身份验证。

如果发生更改，我们将提供对 wiki 的更新和此[问题](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)。

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Azure AD B2C 在 MSAL.Net 中进行缓存 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知问题

MSAL.Net 支持[令牌缓存](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)。 令牌缓存密钥基于标识提供者返回的声明。 当前 MSAL.Net 需要两个声明来生成令牌缓存密钥：  
- `tid` 是 Azure AD 租户 ID， 
- `preferred_username` 

许多 Azure AD B2C 情况下都缺少这两个声明。 

客户的影响在于，尝试显示 "用户名" 字段时，您是否收到 "令牌响应中缺少" 作为值？ 如果是这样，则这是因为 Azure AD B2C 不会在 preferred_username 中返回 IdToken 的值，因为社交帐户和外部标识提供者（Idp）存在限制。 Azure AD 将返回 preferred_username 的值，因为它知道用户是谁，但对于 Azure AD B2C，因为用户可以使用本地帐户、Facebook、Google、GitHub 等登录，因此没有用于 preferred_username 的 Azure AD B2C 的值。 若要取消阻止 MSAL 与 ADAL 的缓存兼容性，我们决定在处理 Azure AD B2C 帐户时，如果 IdToken 不为 preferred_username 返回任何内容，则会使用最终的 "令牌响应中缺少"。 MSAL 必须返回 preferred_username 的值，以跨库维护缓存兼容性。

### <a name="workarounds"></a>之一

#### <a name="mitigation-for-the-missing-tenant-id"></a>缺少的租户 ID 的缓解措施

建议的解决方法是[按策略使用缓存](#acquire-a-token-to-apply-a-policy)

或者，如果使用[B2C 自定义策略](https://aka.ms/ief)，则可以使用 `tid` 声明，因为它提供了向应用程序返回更多声明的功能。 了解有关[声明转换](/azure/active-directory-b2c/claims-transformation-technical-profile)的详细信息

#### <a name="mitigation-for-missing-from-the-token-response"></a>"令牌响应中缺少" 的缓解措施
一种选择是使用 "名称" 声明作为首选用户名。 "返回声明" 列中的此[B2C doc](../../active-directory-b2c/active-directory-b2c-reference-policies.md) > 中介绍了此过程，请选择要在成功配置文件编辑体验后发回到应用程序的授权令牌中返回的声明。 例如，选择 "显示名称"、"邮政编码"。

## <a name="next-steps"></a>后续步骤 

下面的示例提供了有关以交互方式获取用于 Azure AD B2C 应用程序的 MSAL.NET 的令牌的更多详细信息。

| 示例 | 平台 | 描述|
|------ | -------- | -----------|
|[active-directory-b2c-本机](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS，Xamarin Android，UWP | 一个简单的 Xamarin Forms 应用，展示如何使用 MSAL.NET 通过 Azure AD B2C 来对用户进行身份验证，并使用生成的令牌访问一个 Web API。|
