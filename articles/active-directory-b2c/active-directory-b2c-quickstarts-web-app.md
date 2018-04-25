---
title: 快速入门 - 使用 Azure Active Directory B2C 为 ASP.NET 应用程序设置登录 | Microsoft Docs
description: 运行一个使用 Azure Active Directory B2C 提供帐户登录的示例 ASP.NET Web 应用。
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: quickstart
ms.custom: mvc
ms.date: 2/13/2018
ms.author: davidmu
ms.openlocfilehash: 4342e8c58c9bb20580d8428a6c9869f9a3b893cb
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>快速入门：使用 Azure Active Directory B2C 为 ASP.NET 应用程序设置登录

Azure Active Directory (Azure AD) B2C 提供云身份管理来保护应用程序、业务和客户。 应用可以使用 Azure AD B2C 通过开放式标准协议对社交帐户和企业帐户进行身份验证。

在本教程中，请使用启用了 Azure AD B2C 的示例 ASP.NET 应用通过社交标识提供者来登录，并调用受 Azure AD B2C 保护的 Web API。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 带有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。 
* Facebook、Google、Microsoft 或 Twitter 中的社交帐户。

## <a name="download-the-sample"></a>下载示例

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)或克隆示例 Web 应用。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="run-the-app-in-visual-studio"></a>在 Visual Studio 中运行应用

在示例应用程序项目文件夹中，打开 Visual Studio 中的 `B2C-WebAPI-DotNet.sln` 解决方案。

示例解决方案中有两个项目：

**Web 应用示例应用 (TaskWebApp)：**用于创建和编辑任务列表的 Web 应用。 此 Web 应用使用**注册或登录**策略来注册或登录用户。

**Web API 示例应用 (TaskService)：**一个 Web API，支持创建、读取、更新和删除任务列表的功能。 此 Web API 受 Azure AD B2C 保护，可以通过 Web 应用进行调用。

在此快速入门中，请同时运行 `TaskWebApp` 和 `TaskService` 项目。 

1. 在解决方案资源管理器中选择解决方案 `B2C-WebAPI-DotNet`。
2. 在 Visual Studio 菜单中，选择“项目”>“设置启动项目...”。 
3. 选择“多启动项目”单选按钮。
4. 将两个项目的“操作”更改为“启动”。 单击“确定”。

按 **F5** 调试这两个应用程序。 各应用程序分别在自己的浏览器选项卡中打开：

`https://localhost:44316/` - 此页面是 ASP.NET Web 应用程序。 在快速入门中，直接与此应用程序交互。
`https://localhost:44332/` - 此页面是由 ASP.NET Web 应用程序调用的 Web API。

## <a name="create-an-account"></a>创建帐户

单击 ASP.NET Web 应用程序中的“注册/登录”链接，启动基于 Azure AD B2C 策略的“注册或登录”工作流。

![示例 ASP.NET Web 应用](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

此示例支持多个注册选项，包括使用社交标识提供者，或者使用电子邮件地址来创建本地帐户。 对于本快速入门，将使用 Facebook、Google、Microsoft 或 Twitter 社交标识提供者帐户。 

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交标识提供者注册

Azure AD B2C 为示例 Web 应用的虚构品牌“Wingtip Toys”提供了一个自定义登录页。 

1. 要使用社交标识提供者注册，请单击要使用的标识提供者按钮。

    ![登录或注册提供程序](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    请使用社交帐户凭据进行身份验证（登录）并授权应用程序读取社交帐户的信息。 通过授予访问权限，应用程序可以从社交帐户检索个人资料信息，如姓名和城市。 

2. 完成标识提供者的登录进程。 例如，如果你选择 Twitter，请输入你的 Twitter 凭据，然后单击“登录”。

    ![使用社交帐户进行身份验证和授权](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

    你的新 Azure AD B2C 帐户个人资料详细信息将使用你社交帐户中的信息预填充。

3. 更新“显示名称”、“职务”、“城市”字段，并单击“继续”。  输入的值用于 Azure AD B2C 用户帐户个人资料。

    ![新帐户注册个人资料详细信息](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

    你已成功使用示例 Web 应用，该应用使用 Azure AD B2C 策略通过标识提供者进行身份验证并创建 Azure AD B2C 用户帐户。 

## <a name="edit-your-profile"></a>编辑个人资料

Azure Active Directory B2C 提供允许用户更新个人资料的功能。 示例 Web 应用对工作流使用 Azure AD B2C 编辑配置文件策略。 

1. 在 Web 应用程序菜单栏中，单击个人资料名称，并选择“编辑个人资料”编辑创建的个人资料。

    ![编辑个人资料](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. 更改“显示名称”和“城市”。  
3. 单击“继续”更新个人资料。 新的显示名称显示在 Web 应用主页的右上部分。

## <a name="access-a-protected-web-api-resource"></a>访问受保护的 Web API 资源

1. 单击“待办事项列表”输入并修改待办事项列表项。 

2. 在“新建项”文本框中输入文本。 单击“添加”调用受 Azure AD B2C 保护的 Web API，该 API 可添加待办事项列表项。

    ![添加待办事项列表项](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    ASP.NET Web 应用程序在对受保护的 Web API 资源的请求中包括 Azure AD 访问令牌，该请求的目的是针对用户的待办事项列表项执行操作。

你已成功使用 Azure AD B2C 用户帐户对受 Azure AD B2C 保护的 Web API 进行授权调用。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 快速入门或教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用启用了 Azure AD B2C 的示例 ASP.NET 应用执行以下操作：使用自定义登录页登录、使用社交标识提供者登录、创建 Azure AD B2C 帐户，以及调用受 Azure AD B2C 保护的 Web API。 

继续阅读本教程可以了解如何将示例 ASP.NET 配置为使用你自己的 Azure AD B2C 租户。

> [!div class="nextstepaction"]
> [教程：使用 Azure Active Directory B2C 在 ASP.NET Web 应用中进行用户身份验证](active-directory-b2c-tutorials-web-app.md)