---
title: "Azure AD v2 Android 获取已启动 – 设置 |Microsoft 文档"
description: "如何获取访问令牌和调用 Microsoft Graph API 或需要从 Azure Active Directory v2 终结点的访问令牌的 Api 的 Android 接入点"
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
ms.openlocfilehash: a43d7e30a6f4176afba27f0de2c2c116df741080
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="set-up-your-project"></a>设置你的项目

> 希望改为下载此示例的 Android Studio 项目？ [下载的项目](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)并跳到[配置步骤](#create-an-application-express)配置在执行之前的代码示例。


### <a name="create-a-new-project"></a>创建新项目 
1.  打开 Android Studio，请转到：`File` > `New` > `New Project`
2.  将你的应用程序，然后单击`Next`
3.  请务必选择*API 21 或更高版本 (Android 5.0)*单击`Next`
4.  保留`Empty Activity`，单击`Next`，然后`Finish`


### <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>将 Microsoft 身份验证库 (MSAL) 添加到你的项目
1.  在 Android Studio 中，转到：`Gradle Scripts` > `build.gradle (Module: app)`
2.  在以下代码复制并粘贴`Dependencies`:

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### <a name="about-this-package"></a>有关此包

上述程序包将安装 Microsoft 身份验证库 (MSAL)。 MSAL 处理获取、 缓存和刷新用户令牌用于访问受 Azure Active Directory v2 终结点的 Api。
<!--end-collapse-->

## <a name="create-your-applications-ui"></a>创建你的应用程序 UI

1.  打开：`activity_main.xml`下`res` > `layout`
2.  更改从活动布局`android.support.constraint.ConstraintLayout`或其他到`LinearLayout`
3.  添加`android:orientation="vertical"`属性`LinearLayout`节点
4.  复制并粘贴下面的代码插入`LinearLayout`节点，替换当前的内容：

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

