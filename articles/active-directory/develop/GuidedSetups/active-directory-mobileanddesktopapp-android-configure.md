---
title: "Azure AD v2 Android 获取已启动 – 配置 |Microsoft 文档"
description: "如何对 Android 应用程序可以获取访问令牌并调用 Microsoft Graph API 或 Api 需要从 Azure Active Directory v2 终结点的访问令牌"
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
ms.openlocfilehash: 945b09ccdb7537987da33d32d94a3ccacd829ffd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="create-an-application-express"></a>创建应用程序 （速成版）
现在你需要注册你的应用程序中*Microsoft 应用程序注册门户*:
1. 注册你的应用程序通过[Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)
2.  输入你的应用程序和你的电子邮件的名称
3.  请确保选中的引导式安装的选项
4.  按照说明获取应用程序 ID 并将其粘贴到你的代码

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到你的解决方案 （高级）
现在你需要注册你的应用程序中*Microsoft 应用程序注册门户*:
1. 转到[Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入你的应用程序和你的电子邮件的名称 
3. 请确保未选中的引导式安装的选项
4. 单击`Add Platform`，然后选择`Native Application`和命中保存
5.  Open `MainActivity` (under `app` > `java` > *`{host}.{namespace}`*)
6.  替换*[输入应用程序 Id 此处]*开头的行中`final static String CLIENT_ID`与刚刚注册的应用程序 ID:

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
打开`AndroidManifest.xml`(下`app`  >  `manifests`) 添加到的以下活动`manifest\application`节点。 这将会注册`BrowserTabActivity`以允许恢复后完成身份验证应用程序的操作系统：
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
在`BrowserTabActivity`，替换`[Enter the application Id here]`与应用程序 id。
</li>
</ol>
