---
title: 单个和多个帐户公共客户端应用 |Microsoft
description: 单个和多个帐户公共客户端应用的概述。
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: f2ce993b8fbf2a1b04ea4ad9d992ba278dbc964e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701410"
---
# <a name="single-and-multiple-account-public-client-apps"></a>单个和多个帐户公共客户端应用

本文将帮助你了解单一帐户和多帐户公用客户端应用中使用的类型，并将重点放在单个帐户公共客户端应用上。 

Azure Active Directory 身份验证库（ADAL）对服务器建模。  Microsoft 身份验证库（MSAL）改为对客户端应用程序建模。  大多数 Android 应用被视为公共客户端。 公共客户端是不能安全地保存机密的应用程序。  

MSAL 专用化 `PublicClientApplication` 的 API 图面，以简化和阐明一次只允许使用一个帐户的应用程序的开发体验。 `PublicClientApplication` 是 `SingleAccountPublicClientApplication` 和 `MultipleAccountPublicClientApplication`的子类。  下图显示了这些类之间的关系。

![SingleAccountPublicClientApplication UML 类图](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>单个帐户公共客户端应用程序

使用 `SingleAccountPublicClientApplication` 类，可以创建基于 MSAL 的应用程序，该应用只允许一次登录一个帐户。 `SingleAccountPublicClientApplication` 和 `PublicClientApplication` 存在以下不同：

- MSAL 跟踪当前登录的帐户。
  - 如果你的应用使用 broker （Azure 门户应用注册期间的默认设置），并且安装在存在 broker 的设备上，则 MSAL 将验证该帐户在设备上是否仍然可用。
- `signIn` 使你可以从请求范围中显式或单独登录帐户。
- `acquireTokenSilent` 不需要帐户参数。  如果您提供了一个帐户，并且您提供的帐户与 MSAL 跟踪的当前帐户不匹配，则会引发 `MsalClientException`。
- `acquireToken` 不允许用户切换帐户。 如果用户尝试切换到其他帐户，则会引发异常。
- `getCurrentAccount` 返回提供以下内容的 result 对象：
  - 指示帐户是否更改的布尔值。 例如，在从设备中删除某个帐户时，可能会更改该帐户。
  - 上一个帐户。 如果在从设备中删除了帐户或在登录新帐户时需要执行任何本地数据清理，这会很有用。
  - CurrentAccount。
- `signOut` 从设备中删除与客户端关联的任何令牌。  

当安装在设备上的 Android 身份验证代理（如 Microsoft Authenticator 或 Intune 公司门户），并且将应用配置为使用代理时，`signOut` 不会从设备中删除该帐户。

## <a name="single-account-scenario"></a>单个帐户方案

以下伪代码说明了如何使用 `SingleAccountPublicClientApplication`。

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

## <a name="multiple-account-public-client-application"></a>多帐户公用客户端应用程序

`MultipleAccountPublicClientApplication` 类用于创建基于 MSAL 的应用，这些应用允许同时登录多个帐户。 它允许你获取、添加和删除帐户，如下所示：

### <a name="add-an-account"></a>添加帐户

在应用程序中使用一个或多个帐户，方法是调用 `acquireToken` 一次或多次。  

### <a name="get-accounts"></a>获取帐户

- 调用 `getAccount` 获取特定帐户。
- 调用 `getAccounts`以获取应用当前已知的帐户列表。

你的应用程序将无法枚举代理应用已知的设备上的所有 Microsoft 标识平台帐户。 它只能枚举应用程序使用的帐户。  这些函数不会返回已从设备中删除的帐户。

### <a name="remove-an-account"></a>删除帐户

通过使用帐户标识符调用 `removeAccount` 来删除帐户。

如果你的应用程序配置为使用 broker，并在设备上安装了 broker，则在调用 `removeAccount`时，不会将该帐户从代理中删除。  仅删除与客户端关联的令牌。

## <a name="multiple-account-scenario"></a>多帐户方案

下面的伪代码演示如何创建多个帐户应用、如何在设备上列出帐户，以及如何获取令牌。

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
