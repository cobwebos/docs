---
title: Microsoft 标识平台 Android 快速入门 | Azure
description: 了解 Android 应用程序如何才能通过 Microsoft 标识平台终结点调用需要访问令牌的 API。
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f174229da565627c0e5791f53031b338880cb3
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495305"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>快速入门：从 Android 应用登录用户并调用 Microsoft Graph API

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

本快速入门包含了一个代码示例，该示例演示了 Android 应用程序如何将个人、工作和学校帐户进行登录，获取访问令牌以及调用 Microsoft Graph API。

![显示本快速入门生成的示例应用的工作原理](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **先决条件**
> * Android Studio 3+
> * Android 21+ 为必需 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>注册并下载快速入门应用
> 可以使用两个选项来启动快速入门应用程序：
> * [快速][选项 1：注册并自动配置应用，然后下载代码示例](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手动][选项 2：注册并手动配置应用程序和代码示例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>选项 1：注册并自动配置应用，然后下载代码示例
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要注册应用，请执行以下操作：
> 1. 转到新的 [Azure 门户 - 应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs)窗格。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 遵照说明下载内容，并只需单击一下自动配置新应用程序。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>选项 2：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
> 1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
> 1. 导航到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
> 1. 选择“新注册”。
> 1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
>      - 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `Android-Quickstart`。
>      - 点击“`Register`”按钮。
> 1. 转到 `Authentication` > `Redirect URIs` > `Suggested Redirect URIs for public clients`，选择格式为 **msal{AppId}://auth** 的重定向 URI。保存更改。


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>步骤 1：配置应用程序
> 为使此快速入门中的代码示例正常运行，需要将答复 URL 添加为 **msal{AppId}://auth**（其中，{AppId} 是你的应用的应用程序 ID）。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-android/green-check.png) 应用程序已使用这些属性进行配置

#### <a name="step-2-download-the-project"></a>步骤 2：下载项目

* [下载 Android Studio 项目](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>步骤 3：配置项目

> [!div renderon="docs"]
> 如果选择了上面的“选项 1”，则可跳过这些步骤。 在 Android Studio 中打开该项目并运行应用。 

> [!div renderon="portal" class="sxs-lookup"]
> 1. 解压缩该项目并将其在 Android Studio 中打开。
> 1. 在“应用” > “资源” > “原始”中，打开 **auth_config.json**。
> 1. 编辑 **auth_config.json**，替换 `client_id` 和 `tenant_id`：
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. 在“应用” > “清单”中，打开 **AndroidManifest.xml**。
> 1. 将下面的活动添加到 **manifest\application** 节点。 此代码允许 Microsoft 回调你的应用：   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. 解压缩该项目并将其在 Android Studio 中打开。
> 1. 在“应用” > “资源” > “原始”中，打开 **auth_config.json**。
> 1. 编辑 **auth_config.json**，替换 `client_id` 和 `redirect_uri`：
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. 在“应用” > “清单”中，打开 **AndroidManifest.xml**。
> 1. 将下面的活动添加到 **manifest\application** 节点。 此代码片段将注册一个 **BrowserTabActivity**，以允许 OS 在完成身份验证后继续运行应用程序：
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. 将 * 替换为你的应用程序的“应用程序 ID”。 如果需要查找“应用程序 ID”，请转到“概述”页面。

## <a name="more-information"></a>更多信息

请阅读以下各部分来详细了解本快速入门。

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) 是一个库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft 标识平台保护的 API。 可以如下所述使用 Gradle 来安装它：在“依赖项”下的“Gradle 脚本” > “build.gradle (Module: app)”中添加以下内容：

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>MSAL 初始化

可以通过添加以下代码，为 MSAL 添加引用：

```java
import com.microsoft.identity.client.*;
```

然后，使用以下代码对 MSAL 进行初始化：

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |其中： ||
> |---------|---------|
> |`R.raw.auth_config` | 此文件包含应用程序的配置，包括应用/客户端 ID、登录受众以及多个其他的自定义选项。 |

### <a name="requesting-tokens"></a>请求令牌

MSAL 使用两种方法来获取令牌：`acquireToken` 和 `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>以交互方式获取用户令牌

在某些情况下，需要强制用户与 Microsoft 标识平台终结点进行交互，这将导致进行上下文切换以转到系统浏览器，以验证用户的凭据或表示同意。 示例包括：

* 用户首次登录应用程序
* 由于密码已过期，用户可能需要重新输入凭据的情况
* 应用程序正在请求访问用户需要同意的资源的情况
* 需要双重身份验证的情况

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |其中：||
> |---------|---------|
> | `SCOPES` | 包含所请求的作用域（即针对 Microsoft Graph 的 `{ "user.read" }` 或针对自定义 Web API 的 `{ "<Application ID URL>/scope" }`（即 `api://<Application ID>/access_as_user`）） |
> | `getAuthInteractiveCallback` | 在进行身份验证后将控制权返还给应用程序时执行的回调 |

#### <a name="getting-a-user-token-silently"></a>以静默方式获取用户令牌

你不希望在用户每次需要访问资源时都要求其验证其凭据。 大多数情况下，你希望在无需任何用户交互的情况下进行令牌获取和续订。 可以使用 `AcquireTokenSilentAsync` 方法获取令牌，以在初始 `acquireToken` 方法后访问受保护资源：

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |其中：||
> |---------|---------|
> | `SCOPES` | 包含所请求的作用域（即针对 Microsoft Graph 的 `{ "user.read" }` 或针对自定义 Web API 的 `{ "<Application ID URL>/scope" }`（即 `api://<Application ID>/access_as_user`）） |
> | `accounts.get(0)` | 包含你尝试以静默方式获取其令牌的帐户 |
> | `getAuthInteractiveCallback` | 在进行身份验证后将控制权返还给应用程序时执行的回调 |

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
