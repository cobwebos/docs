---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: f55bdc774437d280db51fb69f060bea7dc579a26
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203695"
---
## <a name="set-up-your-project"></a>设置项目

想要改为下载此示例的 Android Studio 项目？ [下载一个项目](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)，并且在执行该项目之前跳到[配置步骤](#register-your-application)来配置代码示例。

### <a name="create-a-new-project"></a>创建新项目

1. 打开 Android Studio，然后选择“文件” > “新建” > “新建项目”。
2. 为应用程序命名，然后选择“下一步”。
3. 选择“API 21 或更新版本 (Android 5.0)”，然后选择“下一步”。
4. 将“空活动”保留原样，选择“下一步”，然后选择“完成”。

### <a name="add-msal-to-your-project"></a>将 MSAL 添加到项目

1. 在 Android Studio 中，选择“Gradle 脚本” > “build.gradle (模块: 应用)”。
2. 在“依存关系”下，粘贴以下代码：

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>关于此包

上述代码中的包将安装 Microsoft 身份验证库 (MSAL)，该库处理所有令牌操作，包括获取、缓存、刷新和删除令牌。 需要令牌才能访问受 Microsoft 标识平台保护的 API。
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>创建应用的 UI

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
