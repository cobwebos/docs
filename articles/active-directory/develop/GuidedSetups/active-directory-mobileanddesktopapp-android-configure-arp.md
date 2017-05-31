---
title: "Azure AD v2 Android 入门 - 配置 | Microsoft Docs"
description: "Android 应用如何从 Azure Active Directory v2 终结点获取访问令牌并调用 Microsoft Graph API 或需要访问令牌的 API"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b034bf99351c5b33c51d9a8401434160bddb15be
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

## <a name="add-the-applications-registration-information-to-your-app"></a>向应用添加应用程序的注册信息

在此步骤中，需要将客户端 ID 添加到项目。

1.    打开 `MainActivity`（在 `app` > `java` > `{host}.{namespace}` 下）
2.    将以 `final static String CLIENT_ID` 开头的行替换为：
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. 打开：`app` > `manifests` > `AndroidManifest.xml`
4. 将下面的活动添加到 `manifest\application` 节点。 此操作会注册 `BrowserTabActivity`，可允许 OS 在完成身份验证后继续运行应用程序：

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

### <a name="what-is-next"></a>后续操作

[测试和验证](active-directory-mobileanddesktopapp-android-test.md)

