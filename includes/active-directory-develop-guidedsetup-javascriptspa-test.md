---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133175"
---
## <a name="test-your-code"></a>测试代码

使用以下环境之一测试代码。

### <a name="test-with-nodejs"></a>使用 Node.js 进行测试

如果使用的不是 Visual Studio，请确保 Web 服务器已启动。

1. 配置服务器侦听基于“index.html”文件位置的 TCP 端口  。 对于 Node.js，通过在命令提示符下从应用程序文件夹运行以下命令，启动 Web 服务器来侦听该端口：

    ```bash
    npm install
    node server.js
    ```
1. 在浏览器中，输入 **http://\<span>\</span>localhost:30662** 或 **http://\<span>\</span>localhost:{port}** ，其中 *port* 是 Web 服务器正在侦听的端口。 应会显示 index.html 文件的内容和“登录”按钮   。

### <a name="test-with-visual-studio"></a>使用 Visual Studio 进行测试

如果使用的是 Visual Studio，请选择项目解决方案，然后选择 F5 以运行项目。 浏览器将打开到 http://<span></span>localhost:{port} 位置，然后应显示“登录”按钮  。

## <a name="test-your-application"></a>测试应用程序

在浏览器加载 index.html 文件后，选择“登录”。   系统将提示你使用 Microsoft 标识平台终结点进行登录：

![JavaScript SPA 帐户登录窗口](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>许可应用程序访问

第一次登录应用程序时，系统会提示你授予其访问配置文件的权限，并让你登录：

![“请求的权限”窗口](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>查看应用程序结果

登录后，你的用户配置文件信息将在页面上显示的 Microsoft Graph API 响应中返回。

![Microsoft Graph API 调用的结果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，在注册门户上注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 例如，Microsoft Graph API 需要 *Calendars.Read* 作用域来列出用户的日历。

若要在应用程序上下文中访问用户的日历，请将 *Calendars.Read* 委派权限添加到应用程序注册信息。 然后，将 *Calendars.Read* 作用域添加到 `acquireTokenSilent` 调用。

>[!NOTE]
>当你增加作用域数量时，可能会提示用户另外进行许可。

如果后端 API 不需要作用域（不建议），则可以将 clientId  用作调用中的作用域来获取令牌。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
