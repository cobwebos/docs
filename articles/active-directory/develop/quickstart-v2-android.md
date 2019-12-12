---
title: Microsoft 标识平台 Android 快速入门 | Azure
description: 了解 Android 应用程序如何才能通过 Microsoft 标识平台终结点调用需要访问令牌的 API。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84603dfa865afe1c7661f49d7dac7374fa62a2a0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920780"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>快速入门：从 Android 应用登录用户并调用 Microsoft Graph API

本快速入门使用一个代码示例来演示 Android 应用程序如何通过 Microsoft 标识平台将个人、工作或学校帐户登录，然后获取访问令牌并调用 Microsoft Graph API。

应用程序必须由 Azure Active Directory 中的应用程序对象表示，以便 Microsoft 标识平台可以与应用程序共享令牌。

> [!div renderon="docs"]
> 为方便起见，该代码示例在 `AndroidManifest.xml` 文件中附带了默认 `redirect_uri` 预配置，因此你不必先注册自己的应用程序对象。 `redirect_uri` 部分取决于应用程序的签名密钥。 示例项目使用签名密钥进行预配置，以使提供的 `redirect_uri` 生效。 若要了解有关注册应用程序对象并将其与应用程序集成的详细信息，请参阅[登录用户和从 Android 应用程序调用 Microsoft Graph](tutorial-v2-android.md) 教程。

![示例应用程序的屏幕截图](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **先决条件**
> * Android Studio 
> * Android 16+

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
> * [下载代码示例](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ### <a name="step-3-configure-your-project"></a>步骤 3：配置项目
> 1. 解压缩该项目并将其在 Android Studio 中打开。
> 2. 在“app” > “src” > “main” > “res” > “raw”中，打开 auth_config_multiple_account.json 并将其替换为以下代码       ：
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "MULTIPLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 3. 在“app” > “源” > “main” > “res” > “raw”中，打开 auth_config_single_account.json 并将其替换为以下代码       ：
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "SINGLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 4. 在“app” > “src” > “main”中，打开 AndroidManifest.xml     。
> 5. 在 manifest\application 节点中，将 activity android:name="com.microsoft.identity.client.BrowserTabActivity" 节点替换为以下内容   ：    
> ```xml
> <!--Intent filter to catch Microsoft's callback after Sign In-->
> <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
>     <intent-filter>
>         <action android:name="android.intent.action.VIEW" />
>         <category android:name="android.intent.category.DEFAULT" />
>         <category android:name="android.intent.category.BROWSABLE" />
>         <!--
>             Add in your scheme/host from registered redirect URI 
>             note that the leading "/" is required for android:path
>         -->
>         <data 
>             android:host="Enter_the_Package_Name"
>             android:path="/Enter_the_Signature_Hash"
>             android:scheme= "msauth" />
>     </intent-filter>
> </activity>
> ```
> 6. 运行应用！   
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
> > 本快速入门支持 Enter_the_Supported_Account_Info_Here。

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
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

可以在示例项目的 build.gradle (Module: app) 中看到以下内容：

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.+'
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

在 `onCreateView()` 的 `auth_config_single_account.json` 中，单个帐户 `PublicClientApplication` 是使用 `auth_config_single_account.json` 文件中存储的配置信息创建的。  通过以下方式初始化要在单帐户 MSAL 应用中使用的 MSAL 库：

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
* 如果撤销许可
* 如果应用显式要求许可
* 应用程序首次请求访问资源时
* 需要 MFA 或其他条件访问策略时

通过涉及用户的 UI 以交互方式获取令牌的代码位于 `callGraphApiInteractiveButton` 单击处理程序的 `initializeUI()` 中的 `SingleAccountModeFragment.java` 内：

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

用于加载帐户的代码位于 `loadAccount()` 中的 `SingleAccountModeFragment.java` 内。  加载用户帐户是一个异步操作，因此，在帐户加载、更改或出错时要处理的回调将传递到 MSAL。  以下代码也会处理在删除了帐户、用户切换到其他帐户等情况时发生的 `onAccountChanged()`。

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

`"client_id"` 已预先配置为使用 Microsoft 维护的应用对象注册。
`"redirect_uri"` 已预先配置为使用随代码示例提供的签名密钥。

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

在 `onCreateView()` 中的 `MultipleAccountModeFragment.java` 文件内，多帐户应用对象 (`IMultipleAccountPublicClientApplication`) 是使用 `auth_config_multiple_account.json file` 中存储的配置信息创建的：

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

多帐户应用通常调用 `getAccounts()` 来选择要用于 MSAL 操作的帐户。 用于加载帐户的代码位于 `loadAccounts()` 中的 `MultipleAccountModeFragment.java` 文件内。  加载用户帐户是一个异步操作。 因此，某个回调会处理帐户已加载、更改或出错时的情况。

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
* 如果撤销许可 
* 如果应用显式要求许可 
* 应用程序首次请求访问资源时
* 需要 MFA 或其他条件访问策略时

多帐户应用通常使用 `acquireToken()` 调用通过涉及用户的 UI 以交互方式获取令牌。  以交互方式获取令牌的代码位于 `callGraphApiInteractiveButton` 单击处理程序的 `initializeUI()` 中的 `MultipleAccountModeFragment.java` 文件内：

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

应用不应该在用户每次请求令牌时都要求他们登录。 如果用户已登录，则 `acquireTokenSilentAsync()` 允许应用以无提示方式请求令牌，如 `callGraphApiSilentButton` 单击处理程序的 `initializeUI()` 中的 `MultipleAccountModeFragment.java` 文件内所示：

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

用于删除帐户以及该帐户的所有已缓存令牌的代码位于“删除帐户”按钮的处理程序的 `initializeUI()` 中的 `MultipleAccountModeFragment.java` 文件内。 在删除帐户之前，需要提供从 `getAccounts()` 和 `acquireToken()` 等 MSAL 方法获取的帐户对象。 由于删除帐户是一个异步操作，因此需提供 `onRemoved` 回调来更新 UI。

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

有关各种字段的说明，请参阅[了解 Android MSAL 配置文件](msal-configuration.md)。

与 [auth_config_single_account.json](#auth_config_single_accountjson) 配置文件不同，此配置文件包含 `"account_mode" : "MULTIPLE"` 而不是 `"account_mode" : "SINGLE"`，因为这是一个多帐户应用。

`"client_id"` 已预先配置为使用 Microsoft 维护的应用对象注册。
`"redirect_uri"` 已预先配置为使用随代码示例提供的签名密钥。

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

## <a name="next-steps"></a>后续步骤

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>了解创建本快速入门中使用的应用程序的步骤

尝试[登录用户并从 Android 应用程序调用 Microsoft Graph](tutorial-v2-android.md)教程，获取如何生成可获取访问令牌并使用该令牌调用 Microsoft Graph API 的 Android 应用程序的分步指南。

> [!div class="nextstepaction"]
> [调用图形 API Android 教程](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>适用于 Android 库 wiki 的 MSAL

阅读有关适用于 Android 的 MSAL 库的详细信息：

> [!div class="nextstepaction"]
> [适用于 Android 库 wiki 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

帮助我们改进 Microsoft 标识平台。 通过完成简短的两问题调查，告诉我们你的想法。

> [!div class="nextstepaction"]
> [Microsoft 标识平台调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
