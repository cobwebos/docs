---
title: 快速入门 - 使用 Azure Active Directory B2C 为单页应用设置登录
description: 运行一个使用 Azure Active Directory B2C 提供帐户登录的示例单页应用程序。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 353cbae59a26f6e1efd566b99a04318321740590
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914433"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>快速入门：使用 Azure Active Directory B2C 为单页应用设置登录

Azure Active Directory (Azure AD) B2C 提供云身份管理来保护应用程序、业务和客户。 应用程序可以使用 Azure AD B2C 通过开放式标准协议对社交帐户和企业帐户进行身份验证。 在本教程中，请使用单页应用程序通过社交标识提供者来登录，并调用受 Azure AD B2C 保护的 Web API。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 带有 ASP.NET 和 Web 开发  工作负荷的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
- 安装 [Node.js](https://nodejs.org/en/download/)
- Facebook、Google 或 Microsoft 中的社交帐户。
- 从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)或克隆示例 Web 应用。

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>运行应用程序

1. 启动服务器，方法是在 Node.js 命令提示符处运行以下命令：

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js 输出在 localhost 上侦听的端口号。

    ```
    Listening on port 6420...
    ```

2. 浏览到应用程序的 URL。 例如，`http://localhost:6420` 。

## <a name="sign-in-using-your-account"></a>使用帐户登录

1. 单击“登录”，启动工作流。 

    ![浏览器中显示的单页应用程序示例应用](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    此示例支持多个注册选项，包括使用社交标识提供者，或者使用电子邮件地址来创建本地帐户。 对于本快速入门，将使用 Facebook、Google 或 Microsoft 社交标识提供者帐户。

2. Azure AD B2C 为示例 Web 应用程序的虚构公司“Fabrikam”提供了一个登录页。 要使用社交标识提供者注册，请单击要使用的标识提供者按钮。

    ![显示标识提供者的“登录”或“注册”页面按钮](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    请使用社交帐户凭据进行身份验证（登录）并授权应用程序读取社交帐户的信息。 通过授予访问权限，应用程序可以从社交帐户检索个人资料信息，如姓名和城市。

3. 完成标识提供者的登录进程。

## <a name="access-a-protected-api-resource"></a>访问受保护的 API 资源

单击“调用 Web API”，使显示名称作为 JSON 对象从 Web API 调用返回。 

![显示 Web API 响应的浏览器中的示例应用程序](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

示例单页应用程序在对受保护的 Web API 资源的请求中包括访问令牌。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 快速入门或教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本快速入门使用示例单页应用程序执行了以下操作：

* 使用自定义登录页登录
* 使用社交标识提供者登录
* 创建 Azure AD B2C 租户
* 调用受 Azure AD B2C 保护的 Web API

通过创建自己的 Azure AD B2C 租户来完成入门。

> [!div class="nextstepaction"]
> [在 Azure 门户中创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)
