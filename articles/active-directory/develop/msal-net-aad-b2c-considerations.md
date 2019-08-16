---
title: Azure AD B2C（适用于 .NET 的 Microsoft 身份验证库）| Azure
description: 了解将 Azure AD B2C 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的具体注意事项。
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
ms.openlocfilehash: 7444ecfd7a59224d0f08390385c508e4ecc40ddd
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532708"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 通过社交标识将用户登录

可以在 [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c) 中使用 MSAL.NET 通过社交标识将用户登录。 Azure AD B2C 是围绕策略这一概念构建的。 在 MSAL.NET 中，指定策略相当于提供颁发机构。

- 实例化公共客户端应用程序时，需要在颁发机构中指定策略。
- 想要应用策略时，需要调用包含 `authority` 参数的 `AcquireTokenInteractive` 的重写。

本页面的内容适用于 MSAL 3.x。 如果你对 MSAL 2.x 感兴趣，请参阅 [MSAL 2.x 中的 Azure AD B2C 细节](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租户和策略的颁发机构

要使用的颁发机构是 `https://login.microsoftonline.com/tfp/{tenant}/{policyName}`，其中：

- `tenant` 是 Azure AD B2C 租户的名称。 
- `policyName` 是要应用的策略的名称（例如，“b2c_1_susi”表示登录/注册策略）。

Azure AD B2C 当前提供的指导原则是使用 `b2clogin.com`作为颁发机构。 例如， `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"` 。 有关详细信息，请参阅[此文档](/azure/active-directory-b2c/b2clogin)。

## <a name="instantiating-the-application"></a>实例化应用程序

生成应用程序时，需要提供颁发机构。

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

## <a name="acquire-a-token-to-apply-a-policy"></a>获取用于应用策略的令牌

在公共客户端应用程序中获取受 Azure AD B2C 保护的 API 的令牌需要使用颁发机构的重写：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

替换为：

- `policy`：前面的字符串之一（例如 `PolicySignUpSignIn`）。
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

目前可以通过调用 `AcquireTokenInteractive` 来应用策略（例如，让最终用户编辑其个人资料或重置其密码）。 如果使用这两种策略，则不会使用返回的令牌/身份验证结果。

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile 和 ResetPassword 策略的特殊情况

若要提供可让最终用户使用社交标识登录，然后编辑其个人资料的体验，可以应用 Azure AD B2C EditProfile 策略。 为此，可以结合该策略的特定颁发机构，在 Prompt 设置为 `Prompt.NoPrompt` 的情况下调用 `AcquireTokenInteractive`，以避免显示帐户选择对话框（因为用户已登录）

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
- 新式标识的核心租户：密码被盗用、重放。 我们的观点是共享机密可能会被截获。 此方法与无密码登录是不兼容的。
- 需要执行 MFA 的用户将无法登录（因为没有交互）。
- 用户无法执行单一登录。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 配置 ROPC 流
在 Azure AD B2C 租户中创建一个新的用户流，然后选择“使用 ROPC 登录”。 这会为租户启用 ROPC 策略。 有关更多详细信息，请参阅[配置资源所有者密码凭据流](/azure/active-directory-b2c/configure-ropc)。

`IPublicClientApplication` 包含一个方法：
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

此方法采用以下参数：
- 要请求其访问令牌的范围。
- 一个用户名。
- 用户的安全字符串密码。

请记得使用包含 ROPC 策略的颁发机构。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流的限制
 - ROPC 流**仅适用于本地帐户**（使用电子邮件或用户名注册到 Azure AD B2C 的帐户）。 如果与 Azure AD B2C (Facebook、Google 等) 支持的任何标识提供程序进行联合, 则此流不起作用。
 - 目前，在从 MSAL 实现 ROPC 流时，**不会从 Azure AD B2C 返回 id_token**。 这意味着无法创建帐户对象，因此，在缓存中，既不存在帐户，也不存在用户。 AcquireTokenSilent 流在此方案中不起作用。 但是，ROPC 不会显示 UI，因此对用户体验没有影响。

## <a name="google-auth-and-embedded-webview"></a>Google 身份验证和嵌入式 Web 视图

如果你是使用 Google 作为标识提供者的 Azure AD B2C 开发人员, 我们建议你使用系统浏览器, 因为 Google 不允许[从 embedded webview 进行身份验证](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前, `login.microsoftonline.com`是 Google 的一个受信任的颁发机构。 使用此权限将适用于嵌入的 web 视图。 但是, `b2clogin.com`使用不是 Google 的受信任的颁发机构, 因此用户将无法进行身份验证。

如果发生更改, 我们将提供对 wiki 的更新和此[问题](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)。

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>在 MSAL.Net 中使用 Azure AD B2C 进行缓存 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知问题

MSAL.Net 支持[令牌缓存](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)。 缓存令牌密钥基于标识提供者返回的声明。 目前，MSAL.Net 需要使用两个声明来生成令牌缓存密钥：  
- `tid`：Azure AD 租户 ID； 
- `preferred_username` 

许多 Azure AD B2C 方案中缺少这两个声明。 

判断客户是否受到影响的依据是，在尝试显示用户名字段时，值是否显示为“在令牌响应中缺失”？ 如果是，原因是 Azure AD B2C 不会在 IdToken 中返回 preferred_username 的值，因为社交帐户和外部标识提供者 (IdP) 存在限制。 Azure AD 将返回 preferred_username 的值, 因为它知道用户是谁, 但对于 Azure AD B2C, 因为用户可以使用本地帐户、Facebook、Google、GitHub 等登录, 因此没有用于 preferred_username 的 Azure AD B2C 的值。 为了阻止 MSAL 实施与 ADAL 的缓存兼容性，我们决定在处理 Azure AD B2C 帐户的过程中，当 IdToken 未返回 preferred_username 的任何值时，在我们一端使用“在令牌响应中缺失”。 MSAL 必须返回 preferred_username 的值才能保持库间的缓存兼容性。

### <a name="workarounds"></a>解决方法

#### <a name="mitigation-for-the-missing-tenant-id"></a>缺少租户 ID 的缓解措施

建议的解决方法是使用[按策略缓存](#acquire-a-token-to-apply-a-policy)

或者, 如果使用`tid` [B2C 自定义策略](https://aka.ms/ief), 则可以使用声明, 因为它提供了向应用程序返回其他声明的功能。 了解有关[声明转换](/azure/active-directory-b2c/claims-transformation-technical-profile)的详细信息

#### <a name="mitigation-for-missing-from-the-token-response"></a>“在令牌响应中缺失”的缓解措施
一种做法是使用“name”声明作为首选用户名。 [B2C 文档](../../active-directory-b2c/active-directory-b2c-reference-policies.md)中提到了该过程 ->“在‘返回声明’列中，选择需要在成功获得配置文件编辑体验后发回到应用程序的授权令牌中返回的声明。 例如，选择‘显示名称’、‘邮政编码’。”

## <a name="next-steps"></a>后续步骤 

以下示例提供了有关使用 MSAL.NET 以交互方式获取 Azure AD B2C 应用程序的令牌的更多详细信息。

| 样本 | 平台 | 描述|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | 一个简单的 Xamarin Forms 应用，演示如何使用 MSAL.NET 通过 Azure AD B2C 对用户进行身份验证，并使用生成的令牌访问 Web API。|
