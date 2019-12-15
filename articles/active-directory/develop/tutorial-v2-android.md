---
title: 登录用户并调用 Microsoft Graph (Android) - Microsoft 标识平台 | Azure
description: 从 Microsoft 标识平台 (Android) 获取访问令牌并调用需要访问令牌的 Microsoft Graph 或 API
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feefc368815b1bfe57b67db2cd94702db799d78
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961551"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>教程：从 Android 应用将用户登录并调用 Microsoft Graph

> [!NOTE]
> 本教程尚未更新为使用适用于 Android 1.0 版的 MSAL 库。 它适用于在本教程中配置的早期版本。

本教程介绍如何将 Android 应用与 Microsoft 标识平台集成。 应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌，并针对 Microsoft Graph API 发出请求。  

> [!div class="checklist"]
> * 将 Android 应用与 Microsoft 标识平台集成
> * 将用户登录
> * 获取用于调用 Microsoft Graph API 的访问令牌
> * 调用 Microsoft Graph API。  

完成本教程后，应用程序将接受个人 Microsoft 帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作或学校帐户进行登录。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="how-this-tutorial-works"></a>本教程工作原理

![显示本教程生成的示例应用的工作原理](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

本教程中的应用会将用户登录并代表他们获取数据。  该数据可通过一个受保护的 API (Microsoft 图形 API) 进行访问，该 API 需要授权并且受 Microsoft 标识平台保护。

更具体说来：

* 你的应用将通过浏览器或 Microsoft Authenticator 和 Intune 公司门户登录用户。
* 最终用户将接受应用程序请求的权限。
* 将为你的应用颁发 Microsoft Graph API 的一个访问令牌。
* 该访问令牌将包括在对 Web API 的 HTTP 请求中。
* 处理 Microsoft Graph 响应。

该示例使用 Android 的 Microsoft 身份验证库 (MSAL) 来实现身份验证：[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)。

 MSAL 将自动续订令牌，在设备上的其他应用之间提供单一登录 (SSO)，并管理帐户。

## <a name="prerequisites"></a>先决条件

* 本教程需要 Android Studio 版本 3.5。

## <a name="create-a-project"></a>创建一个项目

本教程将创建新项目。 如果想要下载完整教程，请[下载代码](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)。

1. 打开 Android Studio，然后选择“启动新的 Android Studio 项目”  。
2. 选择“基本活动”，再选择“下一步”   。
3. 命名应用程序。
4. 保存包名称。 以后需将它输入 Azure 门户中。
5. 将语言从“Kotlin”  更改为“Java”  。
6. 将“最低 API 级别”  设置为 **API 19** 或更高，然后单击“完成”。 
7. 在项目视图的下拉列表中选择“项目”  ，以便显示源和非源的项目文件，然后打开 **app/build.gradle**，将 `targetSdkVersion` 设置为 `28`。

## <a name="register-your-application"></a>注册应用程序

1. 转到 [Azure 门户](https://aka.ms/MobileAppReg)。
2. 打开[“应用注册”边栏选项卡](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)，单击“+新建注册”。 
3. 输入应用的“名称”，然后在不设置重定向  URI 的情况下单击“注册”。 
4. 在显示的窗格的“管理”部分，  选择“身份验证”   > “+ 添加平台”   >   “Android”。 （可能必须选择边栏选项卡顶部附近的“切换到新体验”才能看到此部分）
5. 输入项目的包名称。 如果下载了代码，则该值为 `com.azuresamples.msalandroidapp`。
6. 在“配置 Android 应用”页的“签名哈希”部分，单击“生成开发签名哈希”。    然后复制用于平台的 KeyTool 命令。

   > [!Note]
   > 安装 KeyTool.exe，使其作为 Java 开发工具包 (JDK) 的一部分。 还必须安装 OpenSSL 工具才能执行 KeyTool 命令。

7. 生成由 KeyTool 生成的**签名哈希**。
8. 单击 `Configure` 并保存出现在“Android 配置”页中的“MSAL 配置”   ，以便在稍后配置应用时输入它。  单击“完成”  。

## <a name="build-your-app"></a>生成应用

### <a name="add-your-app-registration"></a>添加应用注册

1. 在 Android Studio 的项目窗格中，导航到 **app\src\main\res**。
2. 右键单击“res”  ，选择“新建”   >   “目录”。 输入 `raw` 作为新目录名称，然后单击“确定”。 
3. 在 **app** > **src** > **main** > **res** > **raw** 中，新建名为 `auth_config.json` 的 JSON 文件，然后粘贴以前保存的 MSAL 配置。 有关详细信息，请参阅 [MSAL 配置](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)。
4. 在 **app** > **src** > **main** > **AndroidManifest.xml** 中，将以下 `BrowserTabActivity` 活动添加到应用程序主体。 该条目允许 Microsoft 在完成身份验证后回调应用程序：

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    将 `android:host=` 值替换为在 Azure 门户中注册的包名称。
    将 `android:path=` 值替换为在 Azure 门户中注册的密钥哈希。 签名哈希不应进行 URL 编码。

5. 在 AndroidManifest.xml  内的 `<application>` 标记上方，添加以下权限：

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>创建应用的 UI

1. 在 Android Studio 项目窗口中导航到 **app** > **src** > **main** > **res** > **layout**，打开 **activity_main.xml**，然后打开“文本”视图。 
2. 更改活动布局，例如，将 `<androidx.coordinatorlayout.widget.CoordinatorLayout` 更改为 `<androidx.coordinatorlayout.widget.DrawerLayout`。 
3. 将 `android:orientation="vertical"` 属性添加到 `LinearLayout` 节点。
4. 将以下代码粘贴到 `LinearLayout` 节点，替换当前内容：

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>将 MSAL 添加到项目

1. 在 Android 项目窗口中导航到 **app** > **src** > **build.gradle**。
2. 在“依赖项”  下，粘贴以下内容：

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3+'
    ```

### <a name="use-msal"></a>使用 MSAL

现在，请在 `MainActivity.java` 中进行更改，以便在应用中添加并使用 MSAL。
在 Android Studio 项目窗口中，导航到 **app** > **src** > **main** > **java** > **com.example.（你的应用）** ，然后打开 `MainActivity.java`。

#### <a name="required-imports"></a>要求的导入

将以下导出添加到 `MainActivity.java` 顶部附近：

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>实例化 MSAL

在 `MainActivity` 类中，我们需要实例化 MSAL 以及关于应用将执行的一些配置，包括我们想要访问的作用域和 Web API。

在 `MainActivity` 类中复制以下变量：

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

将 `onCreate()` 的内容替换为以下代码，以便实例化 MSAL：

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

上面的代码尝试在用户通过 `getAccounts()` 打开应用程序时以静默方式登录，若成功则为 `acquireTokenSilentAsync()`。  在接下来的几节中，我们将为没有登录帐户的情况实现回叫处理程序。

#### <a name="use-msal-to-get-tokens"></a>使用 MSAL 获取令牌

现在，我们可以实现应用的 UI 处理逻辑并通过 MSAL 以交互方式获取令牌。

MSAL 公开了获取令牌的两种主要方法：`acquireTokenSilentAsync()` 和 `acquireToken()`。  

如果有帐户，`acquireTokenSilentAsync()` 将登录用户并获取令牌，而无需任何用户交互。 如果成功，MSAL 会将令牌切换到你的应用，如果失败，将生成 `MsalUiRequiredException`。  如果生成此异常或你希望用户具有交互式登录体验（不一定需要凭据、mfa 或其他条件访问策略），则使用 `acquireToken()`。  

`acquireToken()` 会在尝试登录用户并获取令牌时显示 UI。 但是，它可能会使用浏览器中的会话 Cookie 或 Microsoft Authenticator 中的帐户来提供交互式 SSO 体验。

在 `MainActivity` 类中创建以下三个 UI 方法：

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

添加以下方法来获取当前活动并处理静默和交互式回叫：

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>使用 MSAL 进行注销

接下来，添加注销支持。

> [!Important]
> 使用 MSAL 注销会从应用程序中删除有关用户的所有已知信息，但是用户的设备上仍然有一个活动会话。 如果用户尝试再次登录，则可能会看到登录 UI，但由于设备会话仍处于活动状态，可能无需重新输入其凭据。

若要添加注销功能，请将以下方法添加到 `MainActivity` 类中。 该方法会遍历所有帐户并将其删除：

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>调用 Microsoft Graph API

我们收到令牌后，就可以向 [Microsoft Graph API](https://graph.microsoft.com) 发出请求。访问令牌将位于身份验证回叫的 `onSuccess()` 方法内的 `AuthenticationResult` 中。 要构建授权请求，应用需要将访问令牌添加到 HTTP 标头：

| 标头密钥    | 值                 |
| ------------- | --------------------- |
| 授权 | 持有者 \<access-token> |

请将以下两种方法添加到 `MainActivity` 类中，以调用图形并更新 UI：

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>多帐户应用程序

该应用针对单个帐户方案生成。 MSAL 也支持多帐户方案，但它需要应用的一些额外工作。 需要创建 UI 来帮助用户选择他们想要为每个需要令牌的操作使用的帐户。 或者，你的应用可以通过 `getAccounts()` 方法实现启发式选择要使用的帐户。

## <a name="test-your-app"></a>测试应用

### <a name="run-locally"></a>在本地运行

构建应用并将其部署到测试设备或模拟器。 你应能够登录并获取 Azure AD 或个人 Microsoft 帐户的令牌。

你登录后，此应用将显示从 Microsoft Graph `/me` 终结点返回的数据。

### <a name="consent"></a>同意

任何用户首次登录你的应用时，Microsoft 标识都将提示他们同意所请求的权限。  虽然大多数用户都能够同意，但某些 Azure AD 租户已禁用用户同意功能，这要求管理员代表所有用户同意。 要支持此方案，请在 Azure 门户中注册应用的作用域。

## <a name="clean-up-resources"></a>清理资源

如果不再需要，请删除[注册应用程序](#register-your-application) 步骤中创建的应用对象。

## <a name="get-help"></a>获取帮助

如果对本教程或 Microsoft 标识平台有疑问，请访问[帮助和支持](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)。

帮助我们改进 Microsoft 标识平台。 通过完成简短的两问题调查，告诉我们你的想法。

> [!div class="nextstepaction"]
> [Microsoft 标识平台调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
