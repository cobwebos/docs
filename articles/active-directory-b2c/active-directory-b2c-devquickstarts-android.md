---
title: "Azure Active Directory B2C：从 Android 应用程序调用 Web API | Microsoft 文档"
description: "本文说明如何创建一个可以使用 OAuth 2.0 持有者令牌调用 Node.js Web API 的 Android“待办事项列表”应用。 该 Android 应用和 Web API 都使用 Azure Active Directory B2C 来管理用户标识和验证用户身份。"
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 47300333a7c41ad6f23b20ae0698bee839efd3bb


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C：从 Android 应用程序调用 Web API
> [!WARNING]
> 本教程需要一些重要更新，其具体目的是为了避免使用 ADAL Android for B2C。  我们将在下周发布有关在 Android 应用中使用 Azure AD B2C 的全新说明，建议在此之前先等待几天。  如果只是想要体验一下，请继续阅读以下文章。
>
>

通过使用 Azure Active Directory (Azure AD) B2C，只需几个简短的步骤即可将强大的自助服务标识管理功能添加到 Android 应用和 Web API。 本文说明如何创建一个可以使用 OAuth 2.0 持有者令牌调用 Node.js Web API 的 Android“待办事项列表”应用。 该 Android 应用和 Web API 都使用 Azure AD B2C 来管理用户标识和验证用户身份。

本快速入门文章要求读者拥有受 Azure AD with B2C 保护的 Web API 才能正常完成。 我们针对 .NET 和 Node.js 构建了一个 Web API。 本演练假设已配置 Node.js Web API 示例。 有关详细信息，请参阅 [Azure AD B2C Web API for Node.js 教程](active-directory-b2c-devquickstarts-api-node.md)。

对于需要访问受保护资源的 Android 客户端，Azure AD 提供 Active Directory 身份验证库 (ADAL)。 在本质上，ADAL 的唯一用途就是方便应用程序获取访问令牌。 为了演示这种简便性，我们在本指南中构建了一个 Android 待办事项列表应用程序，它可以：

* 使用 [OAuth 2.0 身份验证协议](https://msdn.microsoft.com/library/azure/dn645545.aspx)获取调用待办事项列表 API 的访问令牌。
* 获取用户的待办事项列表。
* 将用户注销。

> [!NOTE]
> 本文未介绍如何使用 Azure AD B2C 实施登录、注册和配置文件管理， 而是着重介绍如何在对用户身份验证后调用 Web API。 请先阅读 [.NET Web 应用快速入门教程](active-directory-b2c-devquickstarts-web-dotnet.md)（如果尚未这样做），了解 Azure AD B2C 的基本知识。
>
>

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后继续执行本指南中的步骤。

## <a name="create-an-application"></a>创建应用程序
接下来，需要在 B2C 目录中创建应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 在本例中，由于应用与 Web API 构成一个逻辑应用，因此由单个**应用程序 ID** 表示。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。 请务必：

* 在应用程序中包含一个 **Web 应用**/**Web API**
* 在“回复 URL”中输入 `urn:ietf:wg:oauth:2.0:oob`。 它是此代码示例的默认 URL。
* 为应用程序创建 **应用程序机密** 并复制下来。 稍后需要用到此值。 请注意，此值在使用之前必须经过 [XML 转义](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) 。
* 复制分配给应用的 **应用程序 ID** 。 稍后也需要用到此信息。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略
[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 此应用包含三个标识体验：注册、登录和使用 Facebook 登录。  需要按 [策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述，为每个类型创建一个策略。 创建三个策略时，请务必：

* 在注册策略中，选择“显示名称”和其他注册属性。
* 在每个策略中，选择“显示名称”和“对象 ID”应用程序声明。 也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 其前缀应为 `b2c_1_`。  稍后需要用到这些策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建三个策略后，可以开始构建应用。

请注意，本文未介绍如何使用刚刚创建的策略。 若要了解 Azure AD B2C 中策略的工作原理，请先阅读 [.NET Web 应用入门教程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="download-the-code"></a>下载代码
本教程的代码 [保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android)。 若要根据说明构建示例，可以[下载 .zip 文件格式的骨干项目](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)。 也可以克隆骨干项目：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [!NOTE]
> **需要下载骨干项目才能完成本教程。** 由于实现完整运行的 Android 应用程序有其复杂性，因此骨干项目包含的 UX 代码将在完成本教程之后运行。 这样可以节省开发人员的时间。 UX 代码与如何将 B2C 添加到 Android 应用程序的主题无关。
>
>

完成的应用也[作为 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)，或者放在同一存储库的 `complete` 分支中。

若要使用 Maven 构建，可以在顶层使用 `pom.xml`。

1. 遵循[先决条件部分的步骤为 Android 设置 Maven](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)。
2. 使用 SDK 21 设置模拟器。
3. 转到存储库克隆到的根文件夹。
4. 运行命令 `mvn clean install`。
5. 将目录切换到快速入门项目示例 `cd samples\hello`。
6. 运行命令 `mvn android:deploy android:run`。

此时应会看到应用正在启动。 输入测试用户凭据以尝试启动。

除了 Android 存档 (AAR) 包以外，还将提交 Java 存档 (JAR) 包。

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>下载 Android ADAL 并将它添加 Android Studio 工作区
有多个选项可供选择，以在 Android 项目中使用此库：

* 可以使用源代码将此库导入到 Eclipse 并链接到应用程序。
* 如果使用 Android Studio，则可以使用 AAR 包格式并引用二进制文件。

### <a name="option-1-binaries-via-gradle-recommended"></a>选项 1：通过 Gradle 获取二进制文件（建议）
可以从 Maven 中心存储库获取二进制文件。 在 Android Studio 中，可将 AAR 包添加到项目（例如，在 `build.gradle` 中），如下所示：

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>选项 2：通过 Maven 获取 AAR
如果在 Eclipse 中使用 `m2e` 插件，可以在 `pom.xml` 文件中指定依赖项：

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>选项 3：通过 Git 获取源代码（最终手段）
若要通过 Git 获取 SDK 的源代码，请输入：

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

使用分支**收敛**。

## <a name="set-up-your-configuration-file"></a>设置配置文件
使用前面在 B2C 门户中设置的配置来配置 Android 项目。

打开 `helpes/Constants.java` 并填充以下各项的值：

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
* `SCOPES`：传递给服务器的范围，在用户登录时，需要向服务器请求此范围。 在 B2C 预览版中，请传递 `client_id`。 但是，将来此参数预期会更改为 `read scopes`。 到时本文档将会更新。
* `ADDITIONAL_SCOPES`：要用于应用程序的其他范围。 将来预期会使用这些范围。
* `CLIENT_ID`：从门户获取的应用程序 ID。
* `REDIRECT_URL`：预期回传令牌的重定向。
* `EXTRA_QP`：想要以 URL 编码格式传递给服务器的任何额外参数。
* `FB_POLICY`：要调用的策略。 这是本演练最重要的部分。
* `EMAIL_SIGNIN_POLICY`：要调用的策略。 这是本演练最重要的部分。
* `EMAIL_SIGNUP_POLICY`：要调用的策略。 这是本演练最重要的部分。

## <a name="add-references-to-android-adal-to-your-project"></a>在项目中添加对 Android ADAL 的引用
> [!NOTE]
> ADAL Android 使用基于意图的模型调用身份验证。 意图“叠加”在应用上运行。 整个示例（全部使用 ADAL for Android）着重于如何管理意图并传递它们之间的信息。
>
>

首先，将应用程序的分配告知 Android，包括想要使用的意图。 稍后将在本教程中详细说明这些意图。

更新项目的 `AndroidManifest.xml` 文件来包含所有意图：

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

可以看到定义了五个活动。 稍后将使用所有这些活动。

* `AuthenticationActivity`：此活动来自 ADAL，提供登录 Web 视图。
* `LoginActivity`：此活动显示登录策略和每个策略的按钮。
* `SettingsActivity`：可在运行时使用此活动更改应用设置。
* `AddTaskActivity`：可使用此活动将任务添加到 Azure AD 保护的 REST API。
* `ToDoActivity`：这是显示任务的主要活动。

## <a name="create-the-sign-in-activity"></a>创建 sign-in 活动
创建主要活动并将它命名为 `LoginActivity`。

创建名为 `LoginActivity.java` 的文件。

必须初始化活动，并添加一些可控制 UI 的按钮。 如果你以前编写过 Android 代码，则应该熟悉这些操作。

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
现在已创建按钮来调用 `ToDoActivity` 意图（该意图在需要令牌时调用 ADAL）。 这些按钮使用活动作为引用和额外参数。 此额外参数由 `intent.putExtra()` 方法传递。 可以使用 `Constants.java` 中指定的值来定义 `"thePolicy"`。 这可让意图得知在验证期间所要调用的策略。

## <a name="create-the-settings-activity"></a>创建 settings 活动
这是一个填充设置 UI 的活动。

创建名为 `SettingsActivity.java` 的文件来执行简单的创建、读取、更新和删除 (CRUD) 操作。

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//        checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//        checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>创建 add-task 活动
可以使用此活动将任务添加到 REST API 终结点。

创建名为 `AddTaskActivity.java` 的文件并写入以下代码。

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>创建 to-do list 活动
这是最重要的活动。 可以使用此活动从 Azure AD 获取策略的令牌，然后使用该令牌调用任务 REST API 服务器。

创建名为 `ToDoActivity.java` 的文件并写入以下代码。 （稍后将介绍调用。）

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 你可能已注意到，这些代码依赖于尚未编写的方法。 其中包括 `updateLoggedInUser()`、`clearSessionCookie()` 和 `getTasks()`。 本指南稍后将编写这些方法。 暂时可以放心忽略 Android Studio 中的错误。

参数说明：

* `SCOPES`：必需的参数，代表尝试请求访问的范围。 在 B2C 预览中，这等同于 `client_id`，但将来可能会更改。
* `POLICY`：用于验证用户身份的策略。
* `CLIENT_ID`：必需的参数，来自 Azure AD 门户。
* `redirectUri`：可设置为包名称。 对于 `acquireToken` 调用，不需要提供此参数。
* `getUserInfo()`：确定用户是否在缓存中的方式。 该参数还说明在找不到该用户或用户的访问令牌无效时，如何提示用户。 本指南稍后会编写此方法。
* `PromptBehavior.always`：帮助请求凭据以跳过缓存和 Cookie。
* `Callback`：在交换令牌的授权代码后调用。 它有一个 `AuthenticationResult` 对象，其中包含访问令牌、过期日期和 ID 令牌信息。

> [!NOTE]
> Microsoft Intune 公司门户应用提供消息代理组件，其可安装在用户的设备上。 应用在设备上提供所有应用程序的单一登录 (SSO) 访问。 开发人员应做好准备以便使用 Intune。 如果验证器中已创建一个用户帐户，ADAL for Android 将使用代理帐户。 若要使用代理，开发人员需要注册一个特殊的 `redirectUri` 供代理使用。 `redirectUri` 的格式为 msauth://packagename/Base64UrlencodedSignature。 可以使用脚本 `brokerRedirectPrint.ps1` 或 API 调用 `mContext.getBrokerRedirectUri()` 获取应用的 `redirectUri`。 签名与从 Google Play Store 获取的签名证书相关。
>
>

 可以使用以下方法跳过代理用户：

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [!NOTE]
> 为了降低本 B2C 快速入门的复杂性，我们在示例中已选择跳过代理。
>
>

接下来，创建一些帮助器方法，以便于对任务 API 运行验证调用期间单独获取令牌。

在同一个 `ToDoActivity.java` 文件中编写以下代码。

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

此外，添加可将 `AuthenticationResult`（其中包含令牌）“设置”及“连接”到全局 `Constants` 的方法。 即使 `ToDoActivity.java` 在其流中使用 `sResult`，还是需要添加这些方法。 否则，其他活动无法访问令牌来运行任务（例如在 `AddTaskActivity.java` 中添加任务）。

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>创建一个方法用于返回用户标识符
ADAL for Android 以 `UserIdentifier` 对象的形式代表用户。 这可以管理用户。 可以使用此对象来识别调用中是否使用相同的用户。 使用此信息，即可依赖缓存，而不用对服务器进行新的调用。 为了简单起见，我们创建了可返回 `UserIdentifier` 的 `getUserInfo()` 方法。 可以使用此方法配合 `acquireToken()`。 我们还创建了 `getUniqueId()` 方法来返回缓存中 `UserIdentifier` 的 ID。

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>编写帮助器方法
接下来，编写一些帮助器方法来帮助清除 Cookie 并提供 `AuthenticationCallback`。 这些方法纯粹是为了示例而用，确保在调用 `ToDo` 活动时处于初始状态。

在名为 `ToDoActivity.java` 的同一文件中编写以下代码。

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>调用任务 API
准备好让活动捕获令牌后，可以编写 API 来访问任务服务器。

`getTasks` 提供一个表示服务器中任务的数组。

开始使用 `getTasks`。

在名为 `ToDoActivity.java` 的同一文件中编写以下代码。

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

此外，请编写一个方法，以便在首次运行时初始化表。

在名为 `ToDoActivity.java` 的同一文件中编写以下代码。

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

可以看到，这段代码还需要一些其他方法才能运行。 接下来编写这些方法。

### <a name="create-an-endpoint-url-generator"></a>创建终结点 URL 生成器
需要生成要连接的终结点 URL。 在相同的类文件中这样做。

**在名为 `ToDoActivity.java` 的同一文件中**编写以下代码。

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


请注意，在下部分介绍的代码中，将访问令牌添加到请求。

## <a name="write-some-ux-methods"></a>编写一些 UX 方法
Android 需要处理某些回调来操作应用程序。 这些回调是 `createAndShowDialog` 和 `onResume()`。 如果你以前编写过 Android 代码，则应该熟悉这些操作。

在名为 `ToDoActivity.java` 的同一文件中编写以下代码。

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

然后，管理对话回调。

在名为 `ToDoActivity.java` 的同一文件中编写以下代码。

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

现在应该有一个可编译的 `ToDoActivity.java` 文件。 整个项目现在也应该可以编译。

## <a name="run-the-sample-app"></a>运行示例应用
最后，在 Android Studio 或 Eclipse 中生成并运行应用。 注册或登录应用。 为登录的用户创建任务。 注销后，以不同的用户身份重新登录，然后为该用户创建任务。

请注意，这些任务按用户存储在 API 中，因为 API 从它收到的访问令牌中提取用户的标识。

为方便参考，完成的示例[以 .zip 文件形式提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)。 也可以从 GitHub 克隆它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```

## <a name="important-information"></a>重要信息
### <a name="encryption"></a>加密
默认情况下，ADAL 会加密令牌并将其存储在 `SharedPreferences` 中。 可以查看 `StorageHelper` 类来了解详细信息。 Android 引入了 **AndroidKeyStore 4.3(API18)** 来安全存储私钥。 ADAL 将针对 API18 和更高版本使用该组件。 如果想要对较低的 SDK 版本使用 ADAL，需要在 `AuthenticationSettings.INSTANCE.setSecretKey` 中提供机密密钥。

### <a name="session-cookies-in-web-view"></a>Web 视图中的会话 Cookie
在关闭应用程序后，Android Web 视图不会清除会话 Cookie。 可以使用以下示例代码来处理此问题。

```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[详细了解 Cookie](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)。



<!--HONumber=Dec16_HO5-->


