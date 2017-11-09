---
title: "Azure AD v2 iOS 入门 - 测试 | Microsoft Docs"
description: "iOS (Swift) 应用程序如何通过 Azure Active Directory v2 终结点调用需访问令牌的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 4a88096d2b0a23708acdbc1798eac528599b4f71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>测试从 iOS 应用程序查询 Microsoft Graph API

按 `Command` + `R` 以在模拟器中运行该代码。

![示例屏幕截图](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

准备好进行测试后，请点击“调用 Microsoft Graph API”，系统将提示输入用户名和密码。

### <a name="consent"></a>同意
用户首次登录应用程序时，将看到如下所示的许可屏幕，用户需要显式接受：

![许可屏幕](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>预期结果
“日志记录”部分中应显示 Microsoft Graph API 调用返回的用户配置文件信息。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 `user.read` 作用域来读取用户的配置文件。 默认情况下，在我们的注册门户上注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的某些其他 API 及后端服务器的自定义 API 可能需要其他作用域。 例如，对于 Microsoft Graph，需要作用域 `Calendars.Read` 才能列出用户日历。 若要在应用程序上下文中访问用户的日历，则需将 `Calendars.Read` 委派权限添加到应用程序注册信息，然后将 `Calendars.Read` 作用域添加到 `acquireTokenSilent` 调用。 增加作用域数量时，用户可能收到其他许可的提示。

<!--end-collapse-->



