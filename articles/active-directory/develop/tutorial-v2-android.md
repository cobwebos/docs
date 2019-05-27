---
title: Android 入门 - Microsoft 标识平台 | Azure
description: Android 应用如何从 Microsoft 标识平台获取访问令牌并调用 Microsoft 图形 API 或需要访问令牌的 API。
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6112facfc0c10d7a0a0495cd778fa6c3cb6130a7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962155"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>从 Android 应用将用户登录并调用 Microsoft Graph

在本教程中，你将了解如何将 Android 应用集成到 Microsoft 标识平台中。 具体而言，此应用会将用户登录，获取用于调用 Microsoft 图形 API 的访问令牌，并针对 Microsoft 图形 API 发出请求。  

完成本指南后，应用程序将接受个人 Microsoft 帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作或学校帐户进行登录。

## <a name="how-this-tutorial-works"></a>本教程工作原理

![显示本教程生成的示例应用的工作原理](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

此示例中的应用会将用户登录并代表他们获取数据。  此数据可通过需要授权的受保护 API（在本例中为 Microsoft 图形 API）进行访问。

更具体说来：

* 你的应用将通过浏览器或 Microsoft Authenticator 和 Intune 公司门户登录用户。
* 最终用户将接受应用程序请求的权限。 
* 将为你的应用颁发 Microsoft Graph API 的一个访问令牌。
* 该访问令牌将包括在对 Web API 的 HTTP 请求中。
* 处理 Microsoft Graph 响应。

该示例使用 Android 的 Microsoft 身份验证库 (MSAL) 来实现身份验证。MSAL 将自动续订令牌，在设备上的其他应用之间提供 SSO，并管理帐户。

## <a name="prerequisites"></a>先决条件

* 此指导式设置使用 Android Studio。
* 必须使用 Android 16 或更高版本（建议使用版本 19 及更高版本）。

## <a name="library"></a>库

本指南使用以下身份验证库：

|库|说明|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft 身份验证库 (MSAL)|

## <a name="set-up-your-project"></a>设置项目

本教程将创建新项目。 如果想要下载完整教程，请[下载代码](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)。

### <a name="create-a-new-project"></a>创建新项目

1. 打开 Android Studio，然后选择“启动新的 Android Studio 项目”。
    - 如果已打开 Android Studio，请选择“文件” > “新建” > “新建项目”。
2. 将“空活动”保留原样，选择“下一步”。
3. 命名应用，将 `Minimum API level` 设置为“API 19 或更高版本”，点击“完成”。
5. 在 `app/build.gradle` 中，将 `targetedSdkVersion` 设置为 27。 

## <a name="register-your-application"></a>注册应用程序

如接下来的两部分中所述，可以采用两种方式之一注册应用程序。

### <a name="register-your-app"></a>注册应用

1. 转到 [Azure 门户](https://aka.ms/MobileAppReg)，然后选择 `New registration`。 
2. 为你的应用输入名称，然后选择 `Register`。 在此阶段不设置重定向 URI。 
3. 在 `Manage` 部分，转到 `Authentication` > `Add a platform` > `Android`
    - 输入项目的包名称。 如果下载了代码，则该值为 `com.azuresamples.msalandroidapp`。 
    - 输入调试/开发签名哈希。 使用门户中的 KeyTool 命令生成签名哈希。 
4. 点击 `Configure` 并存储“MSAL 配置”以供稍后使用。 

## <a name="build-your-app"></a>生成应用

### <a name="configure-your-android-app"></a>配置 Android 应用

1. 右键单击“res” > “新建” > “文件夹” > “原始资源文件夹”
2. 在“应用” > res” > “原始”中，创建名为 `auth_config.json` 的新 JSON 文件并粘贴“MSAL 配置”。 有关详细信息，请参阅 [MSAL 配置](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)。
   <!-- Workaround for Docs conversion bug -->
3. 在“应用” > “清单” > “AndroidManifest.xml”的下方添加 `BrowserTabActivity` 活动。 该条目允许 Microsoft 在完成身份验证后回调应用程序：

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

    请注意，使用的签名哈希不应在 AndroidManifest.xml 中进行 URL 编码。 

4. 在 AndroidManifest.xml 内的 `<application>` 标记上方，添加以下权限：

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. 在 `BrowserTabActivity` 中，将“包名称”和“签名哈希”替换为 Azure 门户中注册的值。

### <a name="create-the-apps-ui"></a>创建应用的 UI

1. 转到“资源” > “布局”，然后打开 **activity_main.xml**。
2. 将活动布局从 `android.support.constraint.ConstraintLayout` 或其他布局更改为 `LinearLayout`。
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

1. 在 Android Studio 中，选择“Gradle 脚本” > “build.gradle (模块: 应用)”。
2. 在“依存关系”下，粘贴以下代码：

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>使用 MSAL 

接下来的几节将在 `MainAcitivty.java` 内进行更改。 我们将逐步介绍在你的应用中添加和使用 MSAL 所需的每个步骤。

#### <a name="required-imports"></a>要求的导入

将以下导入内容添加到项目中： 

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

#### <a name="instantiating-msal"></a>实例化 MSAL 

在 `MainActivity` 类中，我们需要实例化 MSAL 以及关于应用将执行的一些配置，包括我们想要访问的作用域和 Web API。 

在 `MainActivity` 中复制以下变量：

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

现在要实例化 MSAL，请在 `onCreate(...)` 方法中复制以下代码：

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

上面的代码块尝试在用户通过 `getAccounts(...)` 打开应用程序时以静默方式登录，若成功则为 `acquireTokenSilentAsync(...)`。  在接下来的几节中，我们将为没有登录帐户的情况实现回叫处理程序。 

#### <a name="use-msal-to-get-tokens"></a>使用 MSAL 获取令牌

现在，我们可以实现应用的 UI 处理逻辑并通过 MSAL 以交互方式获取令牌。 

MSAL 公开了获取令牌的两种主要方法：`acquireTokenSilentAsync` 和 `acquireToken`。  

如果有帐户，`acquireTokenSilentAsync` 将登录用户并获取令牌，而无需任何用户交互。 如果成功，MSAL 会将令牌切换到你的应用，如果失败，将生成 `MsalUiRequiredException`。  如果生成此异常或你希望用户具有交互式登录体验（可能需要或可能不需要凭据、mfa 或其他条件访问策略），则可以使用 `acquireToken`。  

`acquireToken` 在尝试登录用户并获取令牌时将始终显示用户界面；但是，它可能会使用浏览器中的会话 cookie 或 Microsoft Authenticator 中的帐户来提供交互式 SSO 体验。 

首先，在 `MainActivity` 类中创建以下三个 UI 方法：

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

接下来，添加一个方法来获取当前活动并处理静默和交互式回叫：

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

接下来，我们将添加对注销应用的支持。 

需要注意的是，使用 MSAL 注销会从此应用程序中删除有关用户的所有已知信息，但是用户的设备上仍然有一个活动会话。 如果用户尝试再次登录，则可能会看到交互，但由于设备会话处于活动状态，可能无需重新输入其凭据。 

要添加注销，请将以下方法复制到应用中，该应用会遍历所有帐户并将其删除：

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

成功获取令牌后，我们可以向 Microsoft 图形 API 发出请求。 访问令牌将位于身份验证回叫的 `onSuccess(...)` 方法内的 `AuthenticationResult` 中。 要构建授权请求，应用需要将访问令牌添加到 HTTP 标头：

| 标头密钥    | 值                 |
| ------------- | --------------------- |
| 授权 | 持有者 <access-token> |

要在代码中执行此操作，请将以下两种方法添加到应用以调用图形并更新 UI： 

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

了解有关 [Microsoft 图形 API](https://graph.microsoft.com) 的详细信息！

#### <a name="multi-account-applications"></a>多帐户应用程序

该应用针对单个帐户方案生成。 MSAL 也支持多帐户方案，但它需要应用的一些额外工作。 需要创建 UI 来帮助用户选择他们想要为每个需要令牌的操作使用的帐户。 或者，你的应用可以通过 `getAccounts(...)` 方法实现启发式选择要使用的帐户。 

## <a name="test-your-app"></a>测试应用

### <a name="run-locally"></a>在本地运行

如果已遵循上述代码，请尝试构建应用并将其部署到测试设备或模拟器。 你应能够登录并获取 Azure AD 或个人 Microsoft 帐户的令牌！ 用户登录后，此应用将显示从 Microsoft Graph `/me` 终结点返回的数据。 

若有任何问题，请随时打开此文档或 MSAL 库中的问题并告知我们。 

### <a name="consent-to-your-app"></a>许可应用

任何用户首次登录你的应用时，Microsoft 标识都将提示他们同意所请求的权限。  虽然大多数用户都能够同意，但某些 Azure AD 租户已禁用用户同意功能，这要求管理员代表所有用户同意。  要支持此方案，请务必在 Azure 门户中注册应用的作用域。

## <a name="help-and-support"></a>帮助和支持

对本教程或 Microsoft 标识平台有任何问题吗？ 请参阅[帮助与支持](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
