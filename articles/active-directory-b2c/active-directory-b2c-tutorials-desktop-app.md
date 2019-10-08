---
title: 教程 - 在本机客户端应用程序中启用身份验证 - Azure Active Directory B2C | Microsoft Docs
description: 有关如何使用 Azure Active Directory B2C 为 .NET 桌面应用程序提供用户登录功能的教程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326335"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 在本机客户端应用程序中启用身份验证

本教程展示了如何在 Windows Presentation Foundation (WPF) 桌面应用程序中使用 Azure Active Directory B2C (Azure AD B2C) 执行用户登录和注册。 应用程序可以使用 Azure AD B2C 通过开放式标准协议对社交帐户、企业帐户和 Azure Active Directory 帐户进行身份验证。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加本机客户端应用程序
> * 将示例配置为使用此应用程序
> * 使用用户流进行登录

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- [创建用户流](tutorial-create-user-flows.md)，以便在应用程序中启用用户体验。
- 安装 [Visual Studio 2019](https://www.visualstudio.com/downloads/)，其中包含 **.NET 桌面开发**与 **ASP.NET 和 Web 开发**工作负荷。

## <a name="add-the-native-client-application"></a>添加本机客户端应用程序

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

记录**应用程序 ID**，以便在以后的步骤中使用。

## <a name="configure-the-sample"></a>配置示例

在本教程中，你将配置一个可从 GitHub 下载的示例。 示例 WPF 桌面应用程序演示了如何在 Azure AD B2C 中注册、登录和调用受保护的 Web API。 从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)、[浏览存储库](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)或克隆示例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

若要更改应用设置，请将 `<your-tenant-name>` 替换为你的租户名称，将 `<application-ID`> 替换为你记下的应用程序 ID。

1. 在 Visual Studio 中打开 `active-directory-b2c-wpf` 解决方案。
2. 在 `active-directory-b2c-wpf` 项目中，打开 **App.xaml.cs** 文件并进行以下更新：

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. 将 **PolicySignUpSignIn** 变量更新为你创建的用户流的名称。

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>运行示例

按 **F5** 生成并运行示例。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

1. 单击“登录”  以用户身份登录。 这将使用 **B2C_1_signupsignin1** 用户流。
2. Azure AD B2C 会显示带注册链接的登录页面。 由于你还没有帐户，因此请单击“立即注册”链接。 
3. 注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还收集用户的密码和请求的属性（在用户流中定义）。

    请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。

    ![作为登录/注册工作流的一部分显示的注册页](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. 单击“创建”，  在 Azure AD B2C 租户中创建本地帐户。

现在，用户可以使用其电子邮件地址登录并使用桌面应用了。

> [!NOTE]
> 如果单击“调用 API”  按钮，将会收到“未经授权”错误。 收到此错误是因为你正在尝试从演示租户访问资源。 因为你的访问令牌仅对你的 Azure AD 租户有效，所以该 API 调用未经授权。 请继续学习下一教程来为你的租户创建受保护的 Web API。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加本机客户端应用程序
> * 将示例配置为使用此应用程序
> * 使用用户流进行登录

> [!div class="nextstepaction"]
> [教程：使用 Azure Active Directory B2C 从桌面应用授予对 Node.js Web API 的访问权限](active-directory-b2c-tutorials-spa-webapi.md)
