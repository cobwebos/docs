---
title: "Azure AD v2 Android 入门 - 安装 | Microsoft Docs"
description: "Android 应用可如何从 Azure Active Directory v2 终结点获取访问令牌并调用 Microsoft Graph API 或需要访问令牌的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 9071ab19844898abf7feb7fc0d84120298bb5eaf
ms.contentlocale: zh-cn


---

## 设置项目
<a id="set-up-your-project" class="xliff"></a>

> 想要改为下载此示例的 Android Studio 项目？ [下载项目](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)并跳到[配置](#create-an-application-express "配置步骤")步骤，在执行代码示例前对其进行配置。


### 创建新项目
<a id="create-a-new-project" class="xliff"></a> 
1.  打开 Android Studio，转到： `File` > `New` > `New Project`
2.  命名应用程序，然后单击 `Next`
3.  务必选择 API 21 或更新版本 (Android 5.0)，然后单击 `Next`
4.  保留 `Empty Activity`，然后依次单击 `Next` 和 `Finish`


### 向项目添加 Microsoft 身份验证库 (MSAL)
<a id="add-the-microsoft-authentication-library-msal-to-your-project" class="xliff"></a>
1.  在 Android Studio 中，转到： `Gradle Scripts` > `build.gradle (Module: app)`
2.  复制以下代码并将其粘贴在 `Dependencies` 下：

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### 关于此包
<a id="about-this-package" class="xliff"></a>

以上包将安装 Microsoft 身份验证库 (MSAL)。 MSAL 负责获取、缓存和刷新用于访问受 Azure Active Directory v2 终结点保护的 API 的用户令牌。
<!--end-collapse-->

## 创建应用程序 UI
<a id="create-your-applications-ui" class="xliff"></a>

1.  在 `res` > `layout` 下打开：`activity_main.xml`
2.  将活动布局从 `android.support.constraint.ConstraintLayout` 或其他布局更改为 `LinearLayout`
3.  向 `LinearLayout` 节点添加 `android:orientation="vertical"` 属性
4.  将以下代码复制并粘贴到 `LinearLayout` 节点，替换当前内容：

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


