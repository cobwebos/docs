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
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133060"
---
## <a name="register-your-application"></a>注册应用程序

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 如果帐户提供访问多个租户的权限，请在右上方选择该帐户，然后将门户会话设置为要使用的 Azure AD 租户。
1. 转到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
1. “注册应用程序”页显示后，请输入应用程序的名称。 
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。  
1. 在“重定向 URI”部分下，从下拉列表中选择“Web”平台，然后将值设置为基于 Web 服务器的应用程序 URL   。 

   有关在 Visual Studio 和 Node.js 中设置和获取重定向 URL 的信息，请参阅下面两个部分。

1. 选择“注册”  。
1. 在应用的“概述”页上，记下“应用程序(客户端) ID”值，供稍后使用   。
1. 本快速入门要求启用[隐式授权流](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md)。 在已注册的应用程序的左窗格中，选择“身份验证”  。
1. 在“高级设置”部分的“隐式授权”下，选中“ID 令牌”和“访问令牌”复选框     。 由于此应用需要将用户登录并调用 API，因此需要 ID 令牌和访问令牌。
1. 选择“保存”。 

> #### <a name="set-a-redirect-url-for-nodejs"></a>设置 Node.js 的重定向 URL
> 对于 Node.js，可以在 *server.js* 文件中设置 Web 服务器端口。 本教程使用端口 30662 作为参考，但是，你可以使用任何其他可用的端口。 
>
> 若要设置应用程序注册信息中的重定向 URL，请切换回“应用程序注册”  窗格，然后执行以下两项操作之一：
>
> - 将 *`http://localhost:30662/`* 设置为“重定向 URL”  。
> - 如果使用的是自定义 TCP 端口，请使用 *`http://localhost:<port>/`* （其中， *\<端口>* 是自定义 TCP 端口号）。
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>设置 Visual Studio 的重定向 URL
> 若要获取 Visual Studio 的重定向 URL，请执行以下操作：
> 1. 在“解决方案资源管理器”  中，选择项目。
>
>    此时将打开“属性”窗口  。 如果该窗口未打开，请按 **F4**。
>
>    ![JavaScriptSPA 项目属性窗口](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. 复制“URL”  的值。
 
> 1. 切换回“应用程序注册”  窗格，然后将已复制的值粘贴为“重定向 URL”  。

#### <a name="configure-your-javascript-spa"></a>配置 JavaScript SPA

1. 向项目设置期间创建的 *index.html* 文件中添加应用程序注册信息。 在文件顶部的 `<script></script>` 标记中添加以下代码：

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    其中：
    - *\<Enter_the_Application_Id_here>* 是已注册的应用程序的“应用程序(客户端) ID”  。
    - *\<Enter_the_Tenant_info_here>* 设置为以下选项之一：
       - 如果应用程序支持“此组织目录中的帐户”  ，请将此值替换为“租户 ID”  或“租户名称”  （例如，*contoso.microsoft.com*）。
       - 如果应用程序支持“任何组织目录中的帐户”  ，请将此值替换为 **organizations**。
       - 如果应用程序支持“任何组织目录中的帐户和个人 Microsoft 帐户”  ，请将此值替换为 **common**。 若要将支持限于“仅限个人 Microsoft 帐户”  ，请将此值替换为 **consumers**。
