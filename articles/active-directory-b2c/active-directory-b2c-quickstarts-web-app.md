---
title: "试用 Azure AD B2C Web 应用程序 | Microsoft Docs"
description: "使用 Azure AD B2C 测试环境进行登录、注册、编辑个人资料和重置密码等用户体验的试用"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 2a51f15fd38a901548dcf4c56922f9715c328463
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-web-application-configured-with-azure-ad-b2c"></a>试用配置了 Azure AD B2C的 Web 应用程序

Azure Active Directory B2C 提供云身份管理来保护应用程序、业务和客户。  本快速入门使用示例待办事项列表应用演示以下操作：

* 使用“注册或登录”策略创建社交标识提供者或使用电子邮箱地址的本地帐户，或者通过它们登录。 
* 调用受 Azure AD B2C 保护的 API 来创建和编辑待办事项。

## <a name="prerequisites"></a>先决条件

* 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - **ASP.NET 和 Web 开发**

* Facebook、Google、Microsoft 或 Twitter 中的社交帐户。 如果没有社交帐户，则必须提供有效的电子邮件地址。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

从 GitHub [下载或克隆示例应用程序](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi)。

## <a name="run-the-app-in-visual-studio"></a>在 Visual Studio 中运行应用

在示例应用程序项目文件夹中，打开 Visual Studio 中的 `B2C-WebAPI-DotNet.sln` 解决方案。 

解决方案由两个项目组成：

* **TaskWebApp** – 一个用户可以管理待办事项列表项的 ASP.NET MVC Web 应用程序。  
* **TaskService** – 一个管理在用户待办事项列表项上执行的所有 CRUD 操作的 ASP.NET Web API 后端。 Web 应用调用此 API 并显示结果。

在此快速入门中，需要同时运行 `TaskWebApp` 和 `TaskService` 项目。 

1. 在解决方案资源管理器中，右键单击解决方案并选择“设置启动项目...”。 
2. 选择“多启动项目”单选按钮。
3. 将两个项目的“操作”更改为“启动”。 单击 **“确定”**。

![在 Visual Studio 中设置启动页](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

选择“调试”>“开始调试”生成并运行两个应用程序。 各应用程序分别在自己的浏览器选项卡中打开：

* `https://localhost:44316/` - 此页面是 ASP.NET Web 应用程序。 在快速入门中，直接与此应用程序交互。
* `https://localhost:44332/` - 此页面是由 ASP.NET Web 应用程序调用的 Web API。

## <a name="create-an-account"></a>创建帐户

单击 ASP.NET Web 应用程序中的“注册/登录”链接启动“注册或登录”工作流。 创建帐户时，可以使用现有社交标识提供者帐户或者电子邮件帐户。

![示例 ASP.NET Web 应用](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交标识提供者注册

要使用社交标识提供者注册，请单击要使用的标识提供者按钮。 如果希望使用电子邮件地址，请跳转到[使用电子邮件地址注册](#sign-up-using-an-email-address)部分。

![登录或注册提供程序](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

需使用社交帐户凭据进行身份验证（登录）并授权应用程序读取社交帐户的信息。 通过授予访问权限，应用程序可以从社交帐户检索个人资料信息，如姓名和城市。 

![使用社交帐户进行身份验证和授权](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

完成标识提供者的登录进程。 例如，单击 Twitter 的“登录”按钮。

新帐户个人资料详细信息已预先填充社交帐户的信息。

![新帐户注册个人资料详细信息](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

更新“显示名称”、“职务”、“城市”字段，并单击“继续”。  输入的值用于 Azure AD B2C 用户帐户个人资料。

你已成功创建使用标识提供者的新 Azure AD B2C 用户帐户。 

下一步：[跳转查看声明](#view-your-claims)部分。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

如果选择不使用社交帐户来提供身份验证，可以使用有效电子邮件地址创建 Azure AD B2C 用户帐户。 Azure AD B2C 本地用户帐户将 Azure Active Directory 用作标识提供者。 要使用电子邮件地址，请单击“没有帐户?立即注册”链接。

![使用电子邮件登录或注册](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-email-web.png)

输入有效的电子邮件地址，然后单击“发送验证码”。 需要一个有效的电子邮件地址，用于接收来自 Azure AD B2C 的验证码。 

输入电子邮件中收到的验证码，然后单击“验证码”。

添加个人资料信息，然后单击“创建”。

![通过使用电子邮件的新帐户注册](media/active-directory-b2c-quickstarts-web-app/sign-up-new-account-profile-email-web.png)

你已成功创建新的 Azure AD B2C 本地用户帐户。

## <a name="reset-your-password"></a>重置密码

如果是使用电子邮件地址创建的帐户，那么 Azure AD B2C 具有允许用户重置密码的功能。 要编辑创建的个人资料，请单击菜单栏中的个人资料名称并选择“重置密码”。

输入电子邮件地址并单击“发送验证码”，验证电子邮件地址。 验证码发送到你的电子邮件地址。

输入电子邮件中收到的验证码，然后单击“验证码”。

电子邮件地址验证后，单击“继续”。

输入新密码，然后单击“继续”。

## <a name="view-your-claims"></a>查看声明

单击 Web 应用程序菜单栏中的“声明”，查看与上一操作相关联的声明。 

![通过使用电子邮件的新帐户注册](media/active-directory-b2c-quickstarts-web-app/view-claims-sign-up-web.png)

在此示例中，上一操作适用于“登录或注册”体验。 请注意，“声明类型”`http://schemas.microsoft.com/claims/authnclassreference` 是 `b2c_1_susi`，表示上一操作是注册或登录。 如果上一操作是密码重置，“声明类型”则为 `b2c_1_reset`。

## <a name="edit-your-profile"></a>编辑个人资料

Azure Active Directory B2C 提供允许用户更新个人资料的功能。 在 Web 应用程序菜单栏中，单击个人资料名称，并选择“编辑个人资料”编辑创建的个人资料。

![编辑个人资料](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

更改“显示名称”和“城市”。  单击“继续”更新个人资料。

![更新配置文件](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

请注意，更改名称后，页面右上角的显示名称更新。 

单击“声明”。 对“显示名称”和“城市”做出的更改反映在声明中。

![查看声明](media/active-directory-b2c-quickstarts-web-app/view-claims-update-web.png)

 请注意，“声明类型”`http://schemas.microsoft.com/claims/authnclassreference` 已更新为 `b2c_1_edit_profile`，表示执行的上一操作是编辑个人资料。 另请注意，姓名和城市分别是新值“Sara S”和“西雅图”。

## <a name="access-a-resource"></a>访问资源

单击“待办事项列表”输入并修改待办事项列表项。 ASP.NET Web 应用程序包括对 Web API 资源的请求（请求对用户待办事项列表项执行操作的权限）中的访问令牌。 

在“新建项”文本框中输入文本。 单击“添加”调用添加待办事项列表项的由 Azure AD B2C 保护的 Web API。

![添加待办事项列表项](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

## <a name="other-scenarios"></a>其他方案

其他试用方案如下：

* 注销应用程序并单击“待办事项列表”。 请注意提示登录的方式以及列表项会被保留。 
* 使用不同类型的帐户创建新帐户。 例如，如果之前使用电子邮件地址创建了帐户，则请使用社交标识提供者。

## <a name="next-steps"></a>后续步骤

下一步是创建自己的 Azure AD B2C 租户并将示例配置为使用租户运行。 

> [!div class="nextstepaction"]
> [在 Azure 门户中创建 Azure Active Directory B2C 租户](active-directory-b2c-get-started.md)