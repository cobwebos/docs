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
ms.openlocfilehash: 6e20ce083c415bced22231835cc616ede8f0dd04
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843306"
---
## <a name="register-your-application"></a>注册应用程序
如接下来的两部分中所述，可以采用两种方式之一注册应用程序。

### <a name="option-1-express"></a>选项 1：快速
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)。
2.  在“应用程序名称”中输入应用程序的名称。

3. 确保选中了“指导式设置”复选框，然后选择“创建”。

4. 按照说明获取应用程序 ID，并将其粘贴到代码中。

### <a name="option-2-advanced"></a>选项 2：高级 
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)。
2. 在“应用程序名称”框中输入应用程序的名称。 

3. 确保未选中“指导式设置”复选框，然后选择“创建”。

4. 依次选择“添加平台”、“本机应用程序”和“保存”。

5. 在“应用” > “java” > “{host}.{namespace}”下，打开 `MainActivity`。 

6.  将“[在此处输入应用程序 ID]”替换为应用程序/客户端 ID：

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. 在“应用” > “清单”下，打开 *AndroidManifest.xml* 文件。

8. 在 `manifest\application` 中，添加以下活动。 `BrowserTabActivity` 活动，它允许 Microsoft 在完成身份验证后回调应用程序：

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
<!-- Workaround for Docs conversion bug -->
9. 在 `BrowserTabActivity` 中，将 `[Enter the application Id here]` 替换为应用程序/客户端 ID。
