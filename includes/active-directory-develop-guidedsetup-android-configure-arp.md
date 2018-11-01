---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0154aac14168c9d897698a15e31b3124b208db46
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142661"
---
## <a name="add-the-applications-registration-to-your-code"></a>将应用程序的注册添加到代码中

在此步骤中，需要将应用程序/客户端 ID 添加到项目。

1. 打开 `MainActivity`（在 `app` > `java` > `{host}.{namespace}` 下）
2. 将以 `final static String CLIENT_ID` 开头的行替换为：
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. 打开：`app` > `manifests` > `AndroidManifest.xml`
4. 将以下活动添加到 `manifest\application`。 `BrowserTabActivity` 允许 Microsoft 在完成身份验证后回调应用程序：

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
