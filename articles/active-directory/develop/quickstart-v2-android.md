---
title: 快速入门：向 Android 应用添加 Microsoft 登录功能 | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍 Android 应用程序如何调用 API，该 API 需要 Microsoft 标识平台颁发的访问令牌。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: 37859a8571355dcd61175d7b1b4d9888e058bf3a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91612891"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>快速入门：从 Android 应用登录用户并调用 Microsoft Graph API

本快速入门使用一个代码示例来演示 Android 应用程序如何通过 Microsoft 标识平台将个人、工作或学校帐户登录，然后获取访问令牌并调用 Microsoft Graph API。 （有关说明，请参阅[示例工作原理](#how-the-sample-works)。）

应用程序必须由 Azure Active Directory 中的应用对象表示，以便 Microsoft 标识平台为应用程序提供令牌。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Android Studio
* Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
>  为使此快速入门中的代码示例正常运行，需要添加与身份验证代理兼容的重定向 URI。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-android/green-check.png) 应用程序已使用这些属性进行了配置
>
> ### <a name="step-2-download-the-project"></a>步骤 2：下载项目
> [!div class="sxs-lookup" renderon="portal"]
> 使用 Android Studio 运行项目。
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
> 我们已经为项目配置了应用属性的值，并且该项目已准备好运行。
> 示例应用将在“单帐户模式”屏幕上启动。  默认情况下，会提供默认范围 **user.read**，在调用 Microsoft Graph API 期间读取你自己的配置文件数据时，将使用该范围。 默认提供 Microsoft Graph API 调用的 URL。 可根据需要更改这两个默认值。
>
> ![显示单帐户和多帐户用法的 MSAL 示例应用](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> 使用应用菜单可在单帐户和多帐户模式之间切换。
>
> 在单帐户模式下，使用工作或家庭帐户登录：
>
> 1. 选择“以交互方式获取图形数据”，以提示用户输入其凭据。  在屏幕底部可以看到调用 Microsoft Graph API 后的输出。
> 2. 登录后，选择“以无提示方式获取图形数据”，以便在不再次提示用户输入凭据的情况下调用 Microsoft Graph API。  在屏幕底部可以看到调用 Microsoft Graph API 后的输出。
>
> 在多帐户模式下，可以重复相同的步骤。  此外，还可以删除登录的帐户，这也会删除该帐户的缓存令牌。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>步骤 1：获取示例应用
>
> [下载代码](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)。
>
> ## <a name="step-2-run-the-sample-app"></a>步骤 2：运行示例应用
>
> 从 Android Studio 的“可用设备”下拉列表中选择仿真器或物理设备，然后运行应用。 
>
> 示例应用将在“单帐户模式”屏幕上启动。  默认情况下，会提供默认范围 **user.read**，在调用 Microsoft Graph API 期间读取你自己的配置文件数据时，将使用该范围。 默认提供 Microsoft Graph API 调用的 URL。 可根据需要更改这两个默认值。
>
> ![显示单帐户和多帐户用法的 MSAL 示例应用](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> 使用应用菜单可在单帐户和多帐户模式之间切换。
>
> 在单帐户模式下，使用工作或家庭帐户登录：
>
> 1. 选择“以交互方式获取图形数据”，以提示用户输入其凭据。  在屏幕底部可以看到调用 Microsoft Graph API 后的输出。
> 2. 登录后，选择“以无提示方式获取图形数据”，以便在不再次提示用户输入凭据的情况下调用 Microsoft Graph API。  在屏幕底部可以看到调用 Microsoft Graph API 后的输出。
>
> 在多帐户模式下，可以重复相同的步骤。  此外，还可以删除登录的帐户，这也会删除该帐户的缓存令牌。

## <a name="how-the-sample-works"></a>示例工作原理
![示例应用的屏幕截图](media/quickstart-v2-android/android-intro.svg)


代码已组织成多个片段，演示如何编写单帐户和多帐户 MSAL 应用。 代码文件的组织方式如下：

| 文件  | 演示  |
|---------|---------|
| MainActivity | 管理 UI |
| MSGraphRequestWrapper  | 使用 MSAL 提供的令牌调用 Microsoft Graph API |
| MultipleAccountModeFragment  | 初始化多帐户应用程序，加载用户帐户，并获取用于调用 Microsoft Graph API 的令牌 |
| SingleAccountModeFragment | 初始化单帐户应用程序，加载用户帐户，并获取用于调用 Microsoft Graph API 的令牌 |
| res/auth_config_multiple_account.json  | 多帐户配置文件 |
| res/auth_config_single_account.json  | 单帐户配置文件 |
| Gradle Scripts/build.grade (Module:app) | 此处添加了 MSAL 库依赖项 |

现在让我们更详细地探讨这些文件，并调用每个文件中 MSAL 特定的代码。

### <a name="adding-msal-to-the-app"></a>将 MSAL 添加到应用

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) 是一个库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft 标识平台保护的 API。 将以下内容添加到“Gradle 脚本” > “build.gradle (Module: app)”中的“Dependencies”下时，Gradle 3.0+ 将安装该库：   

```gradle
implementation 'com.microsoft.identity.client:msal:1.+'
```

可以在示例项目的 build.gradle (Module: app) 中看到以下内容：

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

此代码指示 Gradle 从 Maven Central 下载并生成 MSAL。

### <a name="msal-imports"></a>MSAL 导入

与 MSAL 库相关的导入为 `com.microsoft.identity.client.*`。  例如，你将看到 `import com.microsoft.identity.client.PublicClientApplication;`，它是表示公共客户端应用程序的 `PublicClientApplication` 类的命名空间。

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

此文件演示如何创建单帐户 MSAL 应用并调用 Microsoft Graph API。

单帐户应用仅供单个用户使用。  例如，你可能只使用一个帐户登录到映射应用。

#### <a name="single-account-msal-initialization"></a>单帐户 MSAL 初始化

在 `auth_config_single_account.json` 中的 `onCreateView()` 内，单个帐户 `PublicClientApplication` 是使用 `auth_config_single_account.json` 文件中存储的配置信息创建的。  通过以下方式初始化要在单帐户 MSAL 应用中使用的 MSAL 库：

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>将用户登录

在 `SingleAccountModeFragment.java` 中，将用户登录的代码位于 `signInButton` 单击处理程序中的 `initializeUI()` 内。

在尝试获取令牌之前调用 `signIn()`。 `signIn()` 的行为如同调用 `acquireToken()`，将以交互方式提示用户登录。

将用户登录是一个异步操作。 将传递一个回调用于调用 Microsoft Graph API，并在用户登录后更新 UI：

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>将用户注销

在 `SingleAccountModeFragment.java` 中，将用户注销的代码位于 `signOutButton` 单击处理程序中的 `initializeUI()` 内。  将用户注销是一个异步操作。 将用户注销还会清除该帐户的令牌缓存。 将用户帐户注销后，会创建一个回调来更新 UI：

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>以交互方式或无提示方式获取令牌

为了尽量减少提示用户的次数，你通常会以无提示方式获取令牌。 如果出错，则尝试以交互方式访问令牌。 应用首次调用 `signIn()` 时，它将有效充当 `acquireToken()` 的调用，这会提示用户提供凭据。

在某些情况下，系统可能会提示用户选择其帐户、输入其凭据，或者许可应用请求的权限：

* 用户首次登录到应用程序
* 用户在重置其密码时需输入其凭据。
* 如果许可已撤销
* 如果应用显式要求许可
* 应用程序首次请求访问资源时
* 需要 MFA 或其他条件访问策略时

通过涉及用户的 UI 以交互方式获取令牌的代码位于 `callGraphApiInteractiveButton` 单击处理程序中的 `SingleAccountModeFragment.java` 的 `initializeUI()` 内：

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

如果用户已登录，则 `acquireTokenSilentAsync()` 允许应用以无提示方式请求 `callGraphApiSilentButton` 单击处理程序中的 `initializeUI()` 内显示的令牌：

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>加载帐户

用于加载帐户的代码位于 `SingleAccountModeFragment.java` 中的 `loadAccount()` 内。  加载用户帐户是一个异步操作，因此，在帐户加载、更改或出错时要处理的回调将传递到 MSAL。  以下代码也会处理在删除了帐户、用户切换到其他帐户等情况时发生的 `onAccountChanged()`。

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>调用 Microsoft Graph

用户登录后，将通过 `SingleAccountModeFragment.java` 中定义的 `callGraphAPI()` 的 HTTP 请求调用 Microsoft Graph。 此函数是一个简化示例的包装器，它会执行从 `authenticationResult` 获取访问令牌等某些任务，打包 MSGraphRequestWrapper 的调用，并显示调用结果。

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

这是使用单个帐户的 MSAL 应用的配置文件。

有关这些字段的说明，请参阅[了解 Android MSAL 配置文件](msal-configuration.md)。

请注意 `"account_mode" : "SINGLE"`，它会将此应用配置为使用单个帐户。

`"client_id"` 已预配置为使用 Microsoft 维护的应用对象注册。
`"redirect_uri"` 已预配置为使用代码示例随附的签名密钥。

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

此文件演示如何创建多帐户 MSAL 应用并调用 Microsoft Graph API。

邮件应用就是多帐户应用的一个例子，它允许使用多个用户帐户，例如工作帐户和个人帐户。

#### <a name="multiple-account-msal-initialization"></a>多帐户 MSAL 初始化

在 `MultipleAccountModeFragment.java` 文件中的 `onCreateView()` 内，多帐户应用对象 (`IMultipleAccountPublicClientApplication`) 是使用 `auth_config_multiple_account.json file` 中存储的配置信息创建的：

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

创建的 `MultipleAccountPublicClientApplication` 对象存储在某个类成员变量中，因此可以使用该对象来与 MSAL 库交互，以获取令牌以及加载和删除用户帐户。

#### <a name="load-an-account"></a>加载帐户

多帐户应用通常调用 `getAccounts()` 来选择要用于 MSAL 操作的帐户。 用于加载帐户的代码位于 `MultipleAccountModeFragment.java` 文件中的 `loadAccounts()` 内。  加载用户帐户是一个异步操作。 因此，某个回调会处理帐户已加载、更改或出错时的情况。

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>以交互方式或无提示方式获取令牌

在某些情况下，系统可能会提示用户选择其帐户、输入其凭据，或者许可应用请求的权限：

* 用户首次登录应用程序
* 用户在重置其密码时需输入其凭据。
* 如果许可已撤销
* 如果应用显式要求许可
* 应用程序首次请求访问资源时
* 需要 MFA 或其他条件访问策略时

多帐户应用通常使用 `acquireToken()` 调用通过涉及用户的 UI 以交互方式获取令牌。  以交互方式获取令牌的代码位于 `callGraphApiInteractiveButton` 单击处理程序中的 `MultipleAccountModeFragment.java` 文件的 `initializeUI()` 内：

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

应用不应该在用户每次请求令牌时都要求他们登录。 如果用户已登录，则 `acquireTokenSilentAsync()` 允许应用以无提示方式请求令牌，如 `callGraphApiSilentButton` 单击处理程序的 `MultipleAccountModeFragment.java` 文件的 `initializeUI()` 中所示：

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>删除帐户

用于删除帐户以及该帐户的所有已缓存令牌的代码位于“删除帐户”按钮的处理程序的 `MultipleAccountModeFragment.java` 文件中的 `initializeUI()` 内。 在删除帐户之前，需要提供从 `getAccounts()` 和 `acquireToken()` 等 MSAL 方法获取的帐户对象。 由于删除帐户是一个异步操作，因此需提供 `onRemoved` 回调来更新 UI。

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

这是使用多个帐户的 MSAL 应用的配置文件。

有关各个字段的说明，请参阅[了解 Android MSAL 配置文件](msal-configuration.md)。

与 [auth_config_single_account.json](#auth_config_single_accountjson) 配置文件不同，此配置文件包含 `"account_mode" : "MULTIPLE"` 而不是 `"account_mode" : "SINGLE"`，因为这是一个多帐户应用。

`"client_id"` 已预配置为使用 Microsoft 维护的应用对象注册。
`"redirect_uri"` 已预配置为使用代码示例随附的签名密钥。

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

继续学习 Android 教程，在该教程中，你将生成一个从 Microsoft 标识平台获取访问令牌并使用它来调用 Microsoft Graph API 的 Android 应用。

> [!div class="nextstepaction"]
> [教程：从 Android 应用程序将用户登录并调用 Microsoft Graph](tutorial-v2-android.md)
