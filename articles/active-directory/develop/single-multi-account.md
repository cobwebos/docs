---
title: 单帐户和多帐户公共客户端应用 | Azure
description: 单帐户和多帐户公共客户端应用的概述。
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80881326"
---
# <a name="single-and-multiple-account-public-client-apps"></a>单帐户和多帐户公共客户端应用

本文帮助你了解单帐户和多帐户公共客户端应用中使用的类型，并重点介绍单帐户公共客户端应用。 

Azure Active Directory 身份验证库 (ADAL) 为服务器建模。  而 Microsoft 身份验证库 (MSAL) 则为客户端应用程序建模。  大多数 Android 应用都被视为公共客户端。 公共客户端是无法安全保存机密的应用。  

MSAL 提供专业的 `PublicClientApplication` API 交互面来简化和阐明每次只能使用一个帐户的应用的开发体验。 `PublicClientApplication` 包括 `SingleAccountPublicClientApplication` 和 `MultipleAccountPublicClientApplication` 子类。  下图显示了这些类之间的关系。

![SingleAccountPublicClientApplication UML 类关系图](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>单帐户公共客户端应用程序

使用 `SingleAccountPublicClientApplication` 类可以创建每次只允许登录一个帐户的基于 MSAL 的应用。 `SingleAccountPublicClientApplication` 与 `PublicClientApplication` 的差别体现在以下方面：

- MSAL 跟踪当前已登录的帐户。
  - 如果应用使用中介（在 Azure 门户中进行应用注册期间使用的默认设置），并且安装在具有中介的设备上，则 MSAL 将验证该帐户是否仍在该设备上可用。
- `signIn` 允许通过请求范围单独显式登录帐户。
- `acquireTokenSilent` 不需要帐户参数。  如果确实提供了一个帐户，但该帐户与 MSAL 跟踪的当前帐户不匹配，则会引发 `MsalClientException`。
- `acquireToken` 不允许用户切换帐户。 如果用户尝试切换到其他帐户，则会引发异常。
- `getCurrentAccount` 返回提供以下内容的结果对象：
  - 一个指示帐户是否已更改的布尔值。 例如，从设备中删除某个帐户后，该帐户可能就会更改。
  - 以前的帐户。 从设备中删除了帐户或者登录了新帐户时，如果需要执行任何本地数据清理，这些信息将很有用。
  - currentAccount。
- `signOut` 从设备中删除与客户端关联的所有令牌。  

如果在设备上安装了 Android 身份验证中介（例如 Microsoft Authenticator 或 Intune 公司门户），并且应用已配置为使用该中介，则 `signOut` 不会从设备中删除帐户。

## <a name="single-account-scenario"></a>单帐户方案

以下伪代码演示 `SingleAccountPublicClientApplication` 的用法。

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>多帐户公共客户端应用程序

`MultipleAccountPublicClientApplication` 类用于创建允许同时登录多个帐户的基于 MSAL 的应用。 使用该类可以获取、添加和删除帐户，如下所述：

### <a name="add-an-account"></a>添加帐户

通过调用 `acquireToken` 一次或多次，在应用程序中使用一个或多个帐户。  

### <a name="get-accounts"></a>获取帐户

- 调用 `getAccount` 可获取特定的帐户。
- 调用 `getAccounts` 可获取应用当前已知的帐户列表。

应用无法枚举中介应用已知的设备上的所有 Microsoft 标识平台帐户。 它只能枚举应用使用的帐户。  这些函数不会返回已从设备中删除的帐户。

### <a name="remove-an-account"></a>删除帐户

可以结合帐户标识符调用 `removeAccount` 来删除帐户。

如果应用配置为使用中介，并且设备上已安装了中介，则调用 `removeAccount` 时，不会从中介中删除帐户。  只会删除与客户端关联的令牌。

## <a name="multiple-account-scenario"></a>多帐户方案

以下伪代码演示如何创建多帐户应用、列出设备上的帐户和获取令牌。

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
