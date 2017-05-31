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
ms.openlocfilehash: 842f6667d79d6a2cf3ab6fb7ccff575055585dca
ms.contentlocale: zh-cn


---

## <a name="create-an-application-express"></a>创建应用程序（快速）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)注册应用程序
2.    输入应用程序的名称和你的电子邮件
3.    确保选中“指导式设置”选项
4.    按照说明获取应用程序 ID，并将其粘贴到代码中

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到解决方案（高级）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入应用程序的名称和你的电子邮件 
3. 确保取消选中“指导式设置”选项
4. 单击`Add Platforms`，然后选择`Native Application`并单击“保存”
5.    打开 `MainActivity`（在 `app` > `java` > `{host}.{namespace}` 下）
6.    用刚注册的应用程序 ID 替换行（以 `final static String CLIENT_ID` 开头）中的 [在此处输入应用程序 ID]：

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
打开 `AndroidManifest.xml`（在`app` > `manifests`下）将下面的活动添加到 `manifest\application` 节点。 此操作会注册 `BrowserTabActivity`，可允许 OS 在完成身份验证后继续运行应用程序：
</li>
</ol>

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
<!-- Workaround for Docs conversion bug -->
<ol start="8">
<li>
在 `BrowserTabActivity` 中，用应用程序 ID 替换 `[Enter the application Id here]`。
</li>
</ol>

