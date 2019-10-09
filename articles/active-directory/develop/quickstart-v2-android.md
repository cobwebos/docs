---
title: Microsoft 标识平台 Android 快速入门 | Azure
description: 了解 Android 应用程序如何才能通过 Microsoft 标识平台终结点调用需要访问令牌的 API。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678048"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>快速入门：从 Android 应用登录用户并调用 Microsoft Graph API

本快速入门使用一个代码示例来演示 Android 应用程序如何将个人、工作或学校帐户登录，然后获取访问令牌并调用 Microsoft Graph API。

![示例应用的屏幕截图](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **先决条件**
> * Android Studio 
> * Android 16+ 为必需

## <a name="step-1-get-the-sample-app"></a>步骤 1：获取示例应用

[克隆代码](https://github.com/Azure-Samples/ms-identity-android-java.git)。

## <a name="step-2-register-your-application"></a>步骤 2：注册应用程序

若要注册应用程序对象并手动将该应用程序对象的注册信息添加到示例项目，请执行以下步骤：

1. 转到 [Azure 门户](https://aka.ms/MobileAppReg)。
1. 打开[“应用注册”边栏选项卡](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)，单击“+新建注册”。 
1. 输入应用注册的**名称**，然后在不设置重定向 URI 的情况下单击“注册”。 
1. 在“管理”部分，选择“身份验证” > “+ 添加平台” > “Android”。     （可能需要选择边栏选项卡顶部附近的“尝试新体验”才能看到此屏幕） 
1. 输入项目的**包名称**，即 `com.azuresamples.msalandroidapp`。
1. 在“配置 Android 应用”页的“签名哈希”部分，单击“生成开发签名哈希”，并复制 KeyTool 命令，以便在用于开发 Android 应用的平台中使用。   

   > [!Note]
   > 安装 KeyTool.exe，使其作为 Java 开发工具包 (JDK) 的一部分。 还必须安装 OpenSSL 工具才能执行 KeyTool 命令。  需要指定 keytool 并在路径中添加 OpenSSL\bin 目录。

1. 在终端窗口中运行从门户复制的 keytool 命令。
1. 在门户中的“签名哈希”下输入生成的签名哈希。 
1. 单击 `Configure` 并复制“MSAL 配置”。  下一步骤中，需将其复制并粘贴到配置文件中。 单击“完成”  。

## <a name="step-3-add-your-app-registration"></a>步骤 3：添加应用注册

1. 在 Android Studio 中打开示例项目。
1. 在“app” > “res” > “raw”中，打开“auth_config_multiple_account.json”。      粘贴“MSAL 配置”的内容。 这会从门户添加客户端 ID、租户 ID 和 redirect_uri。 内容如下所示，不过，其中会填充客户端 ID、租户 ID 和 redirect_uri 的值：

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
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

1. 打开“app” > “res” > “raw”，打开“auth_config_single_account.json”，然后粘贴“MSAL 配置”的内容。     内容类似于上面的 **auth_config_multiple_account.json** 文件。
1. 在“app” > “manifests” > “AndroidManifest.xml”中，找到 `BrowserTabActivity` 活动。    该条目允许 Microsoft 在完成身份验证后回调应用程序：

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. 将包名称替换为在 Azure 门户中为 `android:host=` 值注册的内容。  在本例中，它是：`com.azuresamples.msalandroidapp`。

    > [!IMPORTANT]
    > **android:path** 值**必须**包含前导“/”字符，否则该值的下方会出现红线，并且示例应用不会运行。
     
1. 替换前面运行 keytool 后所获得的、并在 Azure 门户中为 `android:path=` 值输入的密钥哈希。 签名哈希不应进行 URL 编码。

## <a name="step-4-run-the-sample-app"></a>步骤 4：运行示例应用

从 Android Studio 的“可用设备”下拉列表中选择仿真器或设备，然后运行应用。 

示例应用将在“单帐户模式”屏幕上启动。  默认情况下，会提供默认范围 **user.read**，在调用 Microsoft Graph API 期间读取你自己的配置文件数据时，将使用该范围。 默认提供 Microsoft Graph API 调用的 URL。 可根据需要更改这两个默认值。

![显示单帐户和多帐户用法的 MSAL 示例应用](./media/quickstart-v2-android/quickstart-sample-app.png)

使用应用菜单可在单帐户和多帐户模式之间切换。

在单帐户模式下，使用工作或家庭帐户登录：

1. 选择“以交互方式获取图形数据”，以提示用户输入其凭据。  在屏幕底部可以看到调用 Microsoft Graph API 后的输出。
2. 登录后，选择“以无提示方式获取图形数据”，以便在不再次提示用户输入凭据的情况下调用 Microsoft Graph API。  在屏幕底部可以看到调用 Microsoft Graph API 后的输出。

在多帐户模式下，可以重复相同的步骤。  此外，还可以删除登录的帐户，这也会删除该帐户的缓存令牌。

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

### <a name="add-msal-to-the-app"></a>将 MSAL 添加到应用

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) 是一个库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft 标识平台保护的 API。 将以下内容添加到“Gradle 脚本” > “build.gradle (Module: app)”中的“Dependencies”下时，Gradle 3.0+ 将安装该库：   

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

可以在示例项目的 build.gradle (Module: app) 中看到以下内容：

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
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

在 `onCreateView()` 中，单个帐户 `PublicClientApplication` 是使用 `auth_config_single_account.json` 文件中存储的配置信息创建的。  通过以下方式初始化要在单帐户 MSAL 应用中使用的 MSAL 库：

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

将用户登录的代码位于 `signInButton` 单击处理程序中的 `initializeUI()` 内。

在尝试获取令牌之前调用 `signIn()`。 `signIn()` 的行为如同调用 `acquireToken()`，将以交互方式提示用户登录。

将用户登录是一个异步操作。 将传递一个回调用于调用 Microsoft Graph API，并在用户登录后更新 UI：

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>将用户注销

将用户注销的代码位于 `signOutButton` 单击处理程序中的 `initializeUI()` 内。  将用户注销是一个异步操作。 将用户注销还会清除该帐户的令牌缓存。 将用户帐户注销后，会创建一个回调来更新 UI：

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

通过涉及用户的 UI 以交互方式获取令牌的代码位于 `callGraphApiInteractiveButton` 单击处理程序中的 `initializeUI()` 内：

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

如果用户已登录，则 `acquireTokenSilentAsync()` 允许应用以无提示方式请求 `callGraphApiSilentButton` 单击处理程序中的 `initializeUI()` 内显示的令牌：

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>加载帐户

用于加载帐户的代码位于 `loadAccount()` 中。  加载用户帐户是一个异步操作，因此，在帐户加载、更改或出错时要处理的回调将传递到 MSAL。  以下代码也会处理在删除了帐户、用户切换到其他帐户等情况时发生的 `onAccountChanged()`。

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

用户登录后，将通过 `callGraphAPI()` 的 HTTP 请求调用 Microsoft Graph。 此函数是一个简化示例的包装器，它会执行从 `authenticationResult` 获取访问令牌等某些任务，打包 MSGraphRequestWrapper 的调用，并显示调用结果。

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

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
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

在 `onCreateView()` 中，多帐户应用对象 (`IMultipleAccountPublicClientApplication`) 是使用 `auth_config_multiple_account.json file` 中存储的配置信息创建的：

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

创建的 `MultipleAccountPublicClientApplication` 对象存储在某个类成员变量中，因此可以使用该对象来与 MSAL 库交互，以获取令牌以及加载和删除用户帐户。

#### <a name="load-an-account"></a>加载帐户

多帐户应用通常调用 `GetAccounts()` 来选择要用于 MSAL 操作的帐户。 用于加载帐户的代码位于 `loadAccount()` 中。  加载用户帐户是一个异步操作。 因此，某个回调会处理帐户已加载、更改或出错时的情况。

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
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

多帐户应用通常使用 `acquireToken()` 调用通过涉及用户的 UI 以交互方式获取令牌。  以交互方式获取令牌的代码位于 `callGraphApiInteractiveButton` 单击处理程序中的 `initializeUI()` 内：

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
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

应用不应该在用户每次请求令牌时都要求他们登录。 如果用户已登录，则 `acquireTokenSilentAsync()` 允许应用以无提示方式请求 `initializeUI()` 中的 `callGraphApiSilentButton` 单击处理程序内显示的令牌：

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

用于删除帐户以及该帐户的所有已缓存令牌的代码位于“删除帐户”按钮的处理程序的 `initializeUI()` 中。 在删除帐户之前，需要提供从 `getAccounts()` 和 `acquireToken()` 等 MSAL 函数获取的帐户对象。 由于删除帐户是一个异步操作，因此需提供 `onRemoved` 回调来更新 UI。

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

这是使用多个帐户的 MSAL 应用的配置文件。

有关这些字段的说明，请参阅[了解 Android MSAL 配置文件](msal-configuration.md)。

与 [auth_config_single_account.json](#auth_config_single_accountjson) 配置文件不同，此配置文件包含 `"account_mode" : "MULTIPLE"` 而不是 `"account_mode" : "SINGLE"`，因为这是一个多帐户应用。

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
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

试用 Android 教程，了解有关构建应用程序和新功能的完整分步指南，包括本快速入门的完整说明。

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
