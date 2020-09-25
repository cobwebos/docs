---
title: 快速入门 - 使用 Azure 通信服务向 Android 应用添加 VOIP 呼叫
description: 本教程将介绍如何使用适用于 Android 的 Azure 通信服务呼叫客户端库
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 658aaf018dd4c231b9bc9fc8c6dda78b2a6f05c0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943549"
---
本快速入门将介绍如何使用适用于 Android 的 Azure 通信服务呼叫客户端库开始呼叫。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Android Studio](https://developer.android.com/studio)，用于创建 Android 应用程序。
- Azure 通信服务的[用户访问令牌](../../access-tokens.md)。
- 已部署的通信服务资源。 [创建通信服务资源](../../create-communication-resource.md)。


## <a name="setting-up"></a>设置


### <a name="create-an-android-app-with-an-empty-activity"></a>使用空活动创建 Android 应用

在 Android Studio 中，选择“启动新的 Android Studio 项目”。

:::image type="content" source="../media/android/studio-new-project.png" alt-text="显示在 Android Studio 中选择了“启动新的 Android Studio 项目”按钮的屏幕截图。":::

在“手机和平板电脑”下选择“空活动”项目模板。

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="显示在“项目模板”屏幕中选择了“空活动”选项的屏幕截图。":::

选择最低客户端库版本为“API 26:Android 8.0 (Oreo)”或更高版本。

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="显示在“项目模板”屏幕中选择了“空活动”选项的屏幕截图 2。":::


### <a name="install-the-package"></a>安装包

<!-- TODO: update with instructions on how to download, install and add package to project -->
找到项目级别 build.gradle，确保将 `mavenCentral()` 添加到 `buildscript` 和 `allprojects` 下的存储库列表中
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
然后，在模块级别 build.gradle 中，将以下行添加到 dependencies 和 android 节

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.1'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>将权限添加到应用程序清单

若要请求进行呼叫所需的权限，必须先在应用程序清单 (`app/src/main/AndroidManifest.xml`) 中声明这些权限。 将文件的内容替换为以下内容：

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="setup-the-layout-for-the-app"></a>为应用设置布局

需要两个输入：被叫方 id 的文本输入，以及用于进行呼叫的按钮。 可以通过设计器或通过编辑布局 xml 来添加这些内容。 使用 id `call_button` 和文本输入 `callee_id` 创建一个按钮。 导航到 (`app/src/main/res/layout/activity_main.xml`) 并将文件的内容替换为以下内容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>创建主活动基架和绑定

创建布局后，可以添加绑定以及活动的基本基架。 活动会处理请求运行时权限、创建呼叫代理以及在按下按钮时进行呼叫。 每个操作都涵盖在自己的部分中。 `onCreate` 方法会进行重写，以调用 `getAllPermissions` 和 `createAgent`，以及为呼叫按钮添加绑定。 这仅在创建活动时发生一次。 有关 `onCreate` 的详细信息，请参阅指南[了解活动生命周期](https://developer.android.com/guide/components/activities/activity-lifecycle)。

导航到 MainActivity.java 并将内容替换为以下代码：

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUser;
import com.azure.android.communication.common.CommunicationUserCredential;
import com.azure.communication.calling.CallAgent;
import com.azure.communication.calling.CallClient;
import com.azure.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }
}

```

### <a name="request-permissions-at-runtime"></a>在运行时请求权限

对于 Android 6.0 及更高版本（API 级别 23）和 `targetSdkVersion` 23 或更高版本，在运行时（而不是在安装应用时）授予权限。 为了支持此功能，可以实现 `getAllPermissions` 以便为每个所需权限调用 `ActivityCompat.checkSelfPermission` 和 `ActivityCompat.requestPermissions`。

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> 设计应用时，请考虑何时应请求这些权限。 应在需要时请求权限，而不是提前请求。 有关详细信息，请参阅 [Android 权限指南。](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>对象模型

以下类和接口处理 Azure 通信服务呼叫客户端库的某些主要功能：

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient 是呼叫客户端库的主入口点。|
| CallAgent | CallAgent 用于启动和管理呼叫。 |
| CommunicationUserCredential | CommunicationUserCredential 用作实例化 CallAgent 的令牌凭据。|

## <a name="create-an-agent-from-the-user-access-token"></a>从用户访问令牌创建代理

借助用户令牌，可以实例化经过身份验证的呼叫代理。 通常，此令牌从具有特定于应用程序的身份验证的服务生成。 有关用户访问令牌的详细信息，请查看[用户访问令牌](../../access-tokens.md)指南。 对于快速入门，请使用为你的 Azure 通信服务资源生成的用户访问令牌替换 `<User_Access_Token>`。

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationUserCredential credential = new CommunicationUserCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>使用呼叫代理开始呼叫

可以通过呼叫代理来进行呼叫，只需提供被叫方 id 的列表和呼叫选项。 对于快速入门，将使用不带视频的默认呼叫选项和来自文本输入的单个被叫方 id。

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUser[] {new CommunicationUser(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>启动应用并呼叫回显机器人

现在可以使用工具栏上的“运行应用”按钮 (Shift+F10) 启动应用。 通过呼叫 `8:echo123` 来验证是否能够进行呼叫。 将播放预先录制的消息，然后将你的消息重复回复给你。

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="显示已完成应用程序的屏幕截图。":::
