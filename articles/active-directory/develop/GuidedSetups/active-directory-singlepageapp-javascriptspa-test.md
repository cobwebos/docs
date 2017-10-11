---
title: "Azure AD v2 JS SPA 设置指南 - 测试 | Microsoft 文档"
description: "JavaScript SPA 应用程序如何才能通过 Azure Active Directory v2 终结点调用需要访问令牌的 API"
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
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: c888760ab311e8ac08b1e625bb837f91047db645
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
## <a name="test-your-code"></a>测试代码

> ### <a name="testing-with-visual-studio"></a>使用 Visual Studio 进行测试
> 如果你使用的是 Visual Studio，请按 `F5` 运行你的项目：随即会打开浏览器并将你指向 *http://localhost: {port}*，然后你会看到“调用 Microsoft Graph API”按钮。

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>使用 Python 或另一台 Web 服务器进行测试
> 如果使用的不是 Visual Studio，请确保已启动 Web 服务器，并且已将其配置为基于包含 *index.html* 文件的文件夹侦听 TCP 端口。 对于 Python，你可以从应用的文件夹中通过在命令提示符处/终端运行项目，开始侦听端口：
> 
> ```bash
> python -m http.server 8080
> ```
>  然后，打开浏览器并键入 http://localhost:8080，或者 http://localhost:{port} - 其中 port 对应于 Web 服务器正在侦听的端口。 你会看到其中有“调用 Microsoft Graph API”按钮的 index.html 页面的内容。

## <a name="test-your-application"></a>测试应用程序

浏览器载入你的 *index.html* 页面后，单击“调用 Microsoft Graph API”按钮。 如果这是第一次，浏览器会将你重定向到 Microsoft Azure Active Directory v2 终结点，然后系统会提示你登录。
 
![示例屏幕截图](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>同意
用户首次登录应用程序时，将看到如下所示的许可屏幕，用户需要接受：

 ![许可屏幕](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>预期结果
将显示 Microsoft Graph API 调用响应返回的用户配置文件信息。
 
 ![结果](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

还将显示有关在“访问令牌”和“ID 令牌声明”框中获取的令牌的基本信息。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 `user.read` 作用域来读取用户的配置文件。 默认情况下，在我们的注册门户上注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的某些其他 API 及后端服务器的自定义 API 可能需要其他作用域。 例如，对于 Microsoft Graph，需要作用域 `Calendars.Read` 才能列出用户日历。 若要在应用程序上下文中访问用户的日历，则需将 `Calendars.Read` 委派权限添加到应用程序注册信息，然后将 `Calendars.Read` 作用域添加到 `acquireTokenSilent` 调用。 增加作用域数量时，用户可能收到其他许可的提示。

如果后端 API 不需要作用域（不推荐），则可以将 `clientId` 用作 `acquireTokenSilent` 和/或 `acquireTokenRedirect` 调用中的作用域。

<!--end-collapse-->
