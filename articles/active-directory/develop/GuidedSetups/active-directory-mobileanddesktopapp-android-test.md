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
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 8523f46d6a352c9a6625ddeacc5abe2b4bbf977e
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


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
Graph API 需要 `user.read` 作用域来读取用户个人资料。 默认情况下，通过我们的注册门户注册的每个应用程序均添加了此作用域。 某些其他 Graph API 及后端服务器的自定义 API 需要其他作用域。 例如，对于 Graph，需要 `Calendars.Read` 才能列出用户的日历。 若要在应用程序环境中访问用户的日历，则需要添加此委派应用程序注册的信息，然后将 `Calendars.Read` 添加到 `AcquireTokenAsync` 调用。 增加作用域数量时，用户可能收到接受其他许可的提示。

如果后端 API 不需要作用域（不推荐），则可以将 `ClientId` 用作 `AcquireTokenAsync` 调用中的作用域。
<!--end-collapse-->

