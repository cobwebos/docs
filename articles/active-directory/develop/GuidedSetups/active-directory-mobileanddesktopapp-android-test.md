---
title: "Azure AD v2 Android 入门 - 测试 | Microsoft Docs"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
## <a name="test-your-code"></a>测试代码

1. 将代码部署到设备/模拟器。
2. 准备好进行测试时，使用 Microsoft Azure Active Directory（组织帐户） 或 Microsoft Account（live.com、outlook.com）帐户登录。 

![示例屏幕截图](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![登录](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>同意
用户首次登陆应用程序时，将看到如下所示的许可屏幕，用户需要显式接受： 

![同意](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>预期结果
应看到调用 Microsoft Graph API“me”终结点（用于获取用户个人资料）的结果 - https://graph.microsoft.com/v1.0/me。 有关常见 Microsoft Graph 终结点的列表，请参阅此[文章](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 `user.read` 作用域来读取用户的配置文件。 默认情况下，在我们的注册门户上注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的某些其他 API 及后端服务器的自定义 API 可能需要其他作用域。 例如，对于 Microsoft Graph，需要作用域 `Calendars.Read` 才能列出用户日历。 若要在应用程序上下文中访问用户的日历，则需将 `Calendars.Read` 委派权限添加到应用程序注册信息，然后将 `Calendars.Read` 作用域添加到 `acquireTokenSilentAsync` 调用。 增加作用域数量时，用户可能收到其他许可的提示。

<!--end-collapse-->

