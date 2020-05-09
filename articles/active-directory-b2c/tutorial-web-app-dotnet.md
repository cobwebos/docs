---
title: 教程：在 Web 应用程序中启用身份验证
titleSuffix: Azure AD B2C
description: 有关如何使用 Azure Active Directory B2C 为 ASP.NET Web 应用程序提供用户登录功能的教程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183307"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 在 Web 应用程序中启用身份验证

本教程介绍如何使用 Azure Active Directory B2C (Azure AD B2C) 在 ASP.NET Web 应用程序中进行用户登录和注册。 应用程序可以使用 Azure AD B2C 通过开放式标准协议对社交帐户、企业帐户和 Azure Active Directory 帐户进行身份验证。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新此应用程序
> * 将示例配置为使用此应用程序
> * 使用用户流进行登录

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* [创建用户流](tutorial-create-user-flows.md)，以便在应用程序中启用用户体验。
* 安装带有 ASP.NET 和 Web 开发  工作负荷的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。

## <a name="update-the-application-registration"></a>更新应用程序注册

在按照先决条件完成的教程中，你已在 Azure AD B2C 中注册了 Web 应用程序。 若要与教程中的示例通信，需添加重定向 URI 并为注册的应用程序创建客户端机密（密钥）。

### <a name="add-a-redirect-uri-reply-url"></a>添加重定向 URI（回复 URL）

你可以使用当前的“应用程序”体验，或者使用我们新推出的统一“应用注册(预览版)”体验来更新应用程序   。 [详细了解此新体验](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
1. 选择“应用程序”，然后选择“webapp1”应用程序。  
1. 在“回复 URL”下添加 `https://localhost:44316`。 
1. 选择“保存”。 
1. 在属性页上，记录应用程序 ID，以便在稍后配置 Web 应用程序时使用。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 依次选择“应用注册(预览版)”、“拥有的应用程序”选项卡，然后选择“webapp1”应用程序    。
1. 选择“身份验证”，然后选择“尝试新体验”（如果显示）   。
1. 在“Web”下，选择“添加 URI”链接，输入 `https://localhost:44316`，然后选择“保存”    。
1. 选择“概述”。 
1. 记录“应用程序（客户端）ID”，以便在稍后配置 Web 应用程序时使用  。

* * *

### <a name="create-a-client-secret"></a>创建客户端机密

接下来，为注册的 Web 应用程序创建客户端机密。 Web 应用程序代码示例在请求令牌时使用此代码来证明其身份。

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>配置示例

在本教程中，你将配置一个可从 GitHub 下载的示例。 该示例使用 ASP.NET 提供简单的待办事项列表。 该示例使用 [Microsoft OWIN 中间件组件](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)。 从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)或克隆该示例。 确保将示例文件提取到文件夹中时，路径的总字符长度不到 260。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

示例解决方案中包含以下两个项目：

* **TaskWebApp** - 创建和编辑任务列表。 该示例使用注册或登录  用户流来注册或登录用户。
* **TaskService** - 支持创建、读取、更新和删除任务列表功能。 该 API 受 Azure AD B2C 的保护，由 TaskWebApp 调用。

将示例更改为使用已在租户中注册的应用程序，包括前面记下的应用程序 ID 和密钥。 此外，配置所创建的用户流。 该示例在 Web.config  文件中将配置值作为设置来定义。

更新 Web.config 文件中的设置以处理用户流：

1. 在 Visual Studio 中打开 **B2C-WebAPI-DotNet** 解决方案。
1. 在 **TaskWebApp** 项目中打开 **Web.config** 文件。
    1. 使用你创建的 Azure AD B2C 租户的名称更新 `ida:Tenant` 和 `ida:AadInstance` 的值。 例如，将 `fabrikamb2c` 替换为 `contoso`。
    1. 将 `ida:ClientId` 的值替换为所记录的应用程序 ID。
    1. 将 `ida:ClientSecret` 的值替换为所记录的密钥。 如果客户端密码包含任何预定义的 XML 实体，例如小于 (`<`)、大于 (`>`)、与号 (`&`) 或双引号 (`"`)，则在将客户端密码添加到 Web.config 之前，必须通过对客户端密码进行 XML 编码来转义这些字符。
    1. 将 `ida:SignUpSignInPolicyId` 的值替换为 `b2c_1_signupsignin1`。
    1. 将 `ida:EditProfilePolicyId` 的值替换为 `b2c_1_profileediting1`。
    1. 将 `ida:ResetPasswordPolicyId` 的值替换为 `b2c_1_passwordreset1`。

## <a name="run-the-sample"></a>运行示例

1. 在解决方案资源管理器中，右键单击 **TaskWebApp** 项目，然后单击“设为启动项目”。 
1. 按 **F5**。 默认浏览器将启动到本地网站地址 `https://localhost:44316/`。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

1. 选择“注册/登录”  ，注册为应用程序用户。 将使用 **b2c_1_signupsignin1** 用户流。
1. Azure AD B2C 会显示带注册链接的登录页面。 由于你还没有帐户，因此请选择“立即注册”。  注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还收集用户的密码和请求的属性（在用户流中定义）。
1. 请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。

    ![作为登录/注册工作流的一部分显示的注册页](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. 选择“创建”，  在 Azure AD B2C 租户中创建本地帐户。

现在，应用程序用户可以使用其电子邮件地址登录并使用 Web 应用程序了。

但是，**待办事项列表**功能会不起作用，直到你完成本系列中的下一个教程[教程：使用 Azure AD B2C 保护 ASP.NET Web API](tutorial-web-api-dotnet.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新此应用程序
> * 将示例配置为使用此应用程序
> * 使用用户流进行登录

现在转到下一个教程，以启用 Web 应用程序的**待办事项列表**功能。 在该教程中，你在自己的 Azure AD B2C 租户中注册 Web API 应用程序，然后修改代码示例，以使用租户进行 API 身份验证。

> [!div class="nextstepaction"]
> [教程：使用 Azure Active Directory B2C 保护 ASP.NET Web API](tutorial-web-api-dotnet.md)
