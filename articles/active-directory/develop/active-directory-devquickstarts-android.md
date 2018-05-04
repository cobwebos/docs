---
title: Azure AD Android 入门 | Microsoft 文档
description: 如何生成一个与 Azure AD 集成以方便登录，并使用 OAuth2.0 调用受 Azure AD 保护 API 的 Android 应用程序。
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 25a908c542bf8fdd8008841a1865cdfb40d847fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-android-getting-started"></a>Azure AD Android 入门
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

如果要开发桌面应用程序，Azure Active Directory (Azure AD) 可让你简单直接地使用用户的本地 Active Directory 帐户对其进行身份验证。 它还可以让应用程序安全地使用 Azure AD 保护的任何 Web API，例如 Office 365 API 或 Azure API。

对于需要访问受保护资源的 Android 客户端，Azure AD 提供 Active Directory 身份验证库 (ADAL)。 在本质上，ADAL 的唯一用途就是方便应用程序获取访问令牌。 为了演示这种简便性，我们将构建一个 Android 待办事项列表应用程序，它可以：

* 使用 [OAuth 2.0 身份验证协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)获取用于调用待办事项列表 API 的访问令牌。
* 获取用户的待办事项列表。
* 将用户注销。

要开始，需要一个可在其中创建用户和注册应用程序的 Azure AD 租户。 如果还没有租户，请[了解如何获取租户](active-directory-howto-tenant.md)。

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>步骤 1：下载并运行 Node.js REST API TODO 示例服务器
Node.js REST API TODO 示例是为了与用于生成 Azure AD 的单租户待办事项 REST API 的现有示例配合使用而专门编写的。 这是本快速入门教程的先决条件。

有关如何设置此先决条件的信息，请参阅[适用于 Node.js 的 Azure Active Directory 示例 REST API 服务](active-directory-devquickstarts-webapi-nodejs.md)。


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>步骤 2：向 Azure AD 租户注册 Web API
Active Directory 支持添加两种类型的应用程序：

- 为用户提供服务的 Web API
- 访问这些 Web API 的应用程序（在 Web 或设备上运行）

在此步骤中，将注册你在本地运行的用于测试此示例的 Web API。 通常，此 Web API 是一个 REST 服务，它提供应用需要访问的功能。 Azure AD 可帮助保护任何终结点。

假设要注册前面引用的 TODO REST API。 但是，这同样适用于希望 Azure Active Directory 帮助保护的任何 Web API。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏上，单击帐户。 在“目录”列表中，选择要在其中注册应用程序的 Azure AD 租户。
3. 在左窗格中，单击“所有服务”，并选择“Azure Active Directory”。
4. 单击“应用注册”，并选择“添加”。
5. 为应用程序输入一个友好的名称（例如“TodoListService”），选择“Web 应用程序和/或 Web API”，并单击“下一步”。
6. 对于登录 URL，输入示例的基 URL。 默认情况下，它是 `https://localhost:8080`。
7. 单击“确定”完成注册。
8. 仍然在 Azure 门户中，转到应用程序页面，找到应用程序 ID 值并复制它。 稍后在配置应用程序时需要此值。
9. 从“设置” -> “属性”页中，更新应用 ID URI - 输入 `https://<your_tenant_name>/TodoListService`。 将 `<your_tenant_name>` 替换为 Azure AD 租户的名称。

## <a name="step-3-register-the-sample-android-native-client-application"></a>步骤 3：注册示例 Android 本机客户端应用程序
在此示例中，必须注册 Web 应用程序。 这会允许应用程序与刚才注册的 Web API 进行通信。 除非注册了应用程序，否则 Azure AD 甚至可能会拒绝应用程序要求进行登录。 这是模型的安全功能的一部分。

假设要注册前面引用的示例应用程序。 但是，此过程同样适用于开发的任何应用。

> [!NOTE]
> 你可能想知道为何要将应用程序和 Web API 放在同一个租户中。 如你可能猜到的那样，可以构建一个从其他租户访问 Azure AD 中注册的外部 API 的应用程序。 如果这样做，系统会提示客户许可使用该应用程序中的 API。 适用于 iOS 的 Active Directory 身份验证库将负责处理此许可。 在了解更高级的功能后，将发现，这是从 Azure 和 Office 以及任何其他服务提供程序访问 Microsoft API 套件所需工作的重要部分。 现在，由于已将 Web API 和应用程序注册到同一个租户下，因此，不会看到任何许可提示。 如果只是在为自己的公司开发要使用的应用程序，则通常就是这种情况。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏上，单击帐户。 在“目录”列表中，选择要在其中注册应用程序的 Azure AD 租户。
3. 在左窗格中，单击“所有服务”，并选择“Azure Active Directory”。
4. 单击“应用注册”，并选择“添加”。
5. 为应用程序输入一个友好的名称（例如“TodoListClient-Android”），选择“本机客户端应用程序”，并单击“下一步”。
6. 对于“重定向 URI”，输入 `http://TodoListClient`。 单击“完成” 。
7. 在应用程序页面中，找到应用程序 ID 值并复制它。 稍后在配置应用程序时需要此值。
8. 在“设置”页上，选择“所需权限”，并选择“添加”。  找到并选择 TodoListService，在“委派的权限”下添加“访问 TodoListService”权限，并单击“完成”。

若要使用 Maven 进行构建，可以在顶层使用 pom.xml：

1. 将此存储库克隆到选择的目录：

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. 遵循[先决条件部分中的步骤为 Android 设置 Maven 环境](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven)。
3. 使用 SDK 19 设置模拟器。
4. 转到存储库克隆到的根文件夹。
5. 运行以下命令：`mvn clean install`
6. 将目录切换到快速入门项目示例：`cd samples\hello`。
7. 运行以下命令：`mvn android:deploy android:run`

   应该会看到应用正在启动。
8. 输入测试用户凭据进行尝试。

除了 AAR 包以外，还将提交 JAR 包。

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>步骤 4：下载 Android ADAL 并将其添加到 Eclipse 工作区
我们提供了多个选项以方便你在 Android 项目中使用 ADAL：

* 可以使用源代码将此库导入到 Eclipse 并链接到应用程序。
* 如果使用的是 Android Studio，则可以使用 AAR 包格式并引用二进制文件。

### <a name="option-1-source-zip"></a>选项 1：源 Zip
若要下载源代码副本，请单击页面右侧的单击“下载 ZIP”。 也可以[从 GitHub 下载](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases)。

### <a name="option-2-source-via-git"></a>选项 2：通过 Git 获取源代码
若要通过 Git 获取 SDK 的源代码，请键入：

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>选项 3：通过 Gradle 获取二进制文件
可以从 Maven 中心存储库获取二进制文件。 在 Android Studio 中，可以按以下方式在项目中包括 AAR 包：

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>选项 4：通过 Maven 获取 AAR
如果使用的是 M2Eclipse 插件，可以在 pom.xml 文件中指定依赖关系：

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>选项 5：libs 文件夹中的 JAR 包
可以从 maven 存储库获取 JAR 文件并将其放入项目的 **libs** 文件夹中。 同样，还需要将所需资源复制到项目，因为 JAR 包未包括它们。

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>步骤 5：在项目中添加对 Android ADAL 的引用
1. 添加对项目的引用，并将其指定为 Android 库。 如果不确定如何执行此操作，可以在 [Android Studio 站点](http://developer.android.com/tools/projects/projects-eclipse.html)上获取详细信息。
2. 在项目设置中添加用于调试的项目依赖关系。
3. 更新项目的 AndroidManifest.xml 文件以包括：

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. 在主要活动中创建 AuthenticationContext 的实例。 有关此调用的详细信息不在本主题的范围内，但可以通过查看 [Android 本机客户端示例](https://github.com/AzureAD/azure-activedirectory-library-for-android)来快速入门。 在下面的示例中，SharedPreferences 是默认缓存，机构采用 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` 形式：

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. 复制此代码块，以便在用户输入凭据并收到授权代码后处理 AuthenticationActivity 的结束：

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. 若要请求令牌，可以定义一个回调：

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. 最后，使用该回调来请求令牌：

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

下面是参数说明：

* *resource* 是必需的，它是你尝试访问的资源。
* *clientid* 是必需的，它来自 Azure AD。
* *RedirectUri* 不是必需的，对于 acquireToken 调用，不需要提供此参数。 可以将其设置为包名称。
* *PromptBehavior* 可帮助请求凭据以跳过缓存和 Cookie。
* 在交换令牌的授权代码后，将调用 *callback*。 它具有一个包含访问令牌、过期日期和 ID 令牌信息的 AuthenticationResult 对象。
* *acquireTokenSilent* 是可选的。 可以调用它来处理缓存和令牌刷新。 它还提供同步版本。 它接受 *userId* 作为参数。

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

使用本演练后，应该会获得与 Azure Active Directory 成功集成所需的项目。 有关此工作的更多示例，请访问 GitHub 上的 AzureADSamples/ 存储库。

## <a name="important-information"></a>重要信息

### <a name="broker"></a>代理
Intune 公司门户或 Microsoft Authenticator 应用提供了代理组件。 帐户是在 AccountManager 中创建的。 帐户类型为“com.microsoft.workaccount”。 AccountManager 仅允许单个 SSO 帐户。 针对其中一个应用完成设备质询后，它会为此用户创建一个 SSO Cookie。

若要详细了解如何使用代理进行配置，请查看[代理 wiki 文章](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker)。 

### <a name="authority-url-and-ad-fs"></a>机构 URL 和 AD FS
Active Directory 联合身份验证服务 (AD FS) 不会识别为生产 STS，因此，需要关闭实例发现，并在 AuthenticationContext 构造函数中传递 false。

机构 URL 需要 STS 实例和[租户名称](https://login.microsoftonline.com/yourtenant.onmicrosoft.com)。

### <a name="querying-cache-items"></a>查询缓存项
ADAL 在 SharedPreferences 中提供了默认缓存，以及一些简单的缓存查询函数。 可以使用以下命令从 AuthenticationContext 中获取当前缓存：

    ITokenCacheStore cache = mContext.getCache();

还可以提供缓存实现（如果想要对其进行自定义）。

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>提示行为
ADAL 提供了用于指定提示行为的选项。 如果刷新令牌无效并且需要用户凭据，则 PromptBehavior.Auto 会显示 UI。 PromptBehavior.Always 会跳过缓存使用并始终显示 UI。

### <a name="silent-token-request-from-cache-and-refresh"></a>来自缓存和刷新的无提示令牌请求
静默令牌请求不使用 UI 弹出并且不要求执行操作。 它将从缓存返回一个令牌（如果有）。 如果令牌已过期，此方法会尝试对其进行刷新。 如果刷新令牌已过期或失败，它会返回 AuthenticationException。

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

还可以使用此方法执行同步调用。 可以将回调设置为 null，或使用 acquireTokenSilentSync。

### <a name="diagnostics"></a>诊断
下面是用来诊断问题的信息的主要来源：

* 例外
* 日志
* 网络跟踪

请注意，相关性 ID 是在库中进行诊断的关键所在。 如果想要在代码中将 ADAL 请求关联到其他操作，可以基于每个请求设置相关性 ID。 如果未设置相关性 ID，则 ADAL 将生成一个随机相关性 ID。 然后，会为所有日志消息和网络调用标上该相关性 ID。 每发出一个请求，自我生成的 ID 都会更改。

#### <a name="errors--exceptions"></a>错误和异常
异常是首选的诊断信息。 我们将尝试提供有用的错误消息。 如果发现某个错误消息没有作用，请记录相应的问题并告诉我们。 请提供设备信息，例如型号和 SDK 编号。

若要详细了解你的应用应当处理哪些错误，请查看[错误处理最佳做法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-adal-error-handling)。 

#### <a name="logs"></a>日志
可以将库配置为生成有助于诊断问题的日志消息。 要配置日志记录，可以执行以下调用以配置一个回调，ADAL 将使用该回调来移交它所生成的每条日志消息。

若要启用日志记录，请查看[日志记录 wiki 文章](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging)。

### <a name="session-cookies-in-webview"></a>WebView 中的会话 Cookie
关闭应用后，Android WebView 不会清除会话 cookie。 可以使用以下示例代码处理此 cookie：

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

有关 cookie 的详细信息，请参阅 [Android 站点上的 CookieSyncManager 信息](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)。

### <a name="ntlm-dialog-box"></a>NTLM 对话框
ADAL 版本 1.1.0 支持通过 WebViewClient 中的 onReceivedHttpAuthRequest 事件处理的 NTLM 对话框。 可以为该对话框自定义布局和字符串。

### <a name="cross-app-sso"></a>跨应用 SSO
了解[如何使用 ADAL 在 Android 上启用跨应用 SSO](active-directory-sso-android.md)。  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
