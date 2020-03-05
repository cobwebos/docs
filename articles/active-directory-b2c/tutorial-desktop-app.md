---
title: 教程：在本机客户端应用程序中对用户进行身份验证
titleSuffix: Azure AD B2C
description: 有关如何使用 Azure Active Directory B2C 为 .NET 桌面应用程序提供用户登录功能的教程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186193"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 在本机桌面客户端中对用户进行身份验证

本教程展示了如何在 Windows Presentation Foundation (WPF) 桌面应用程序中使用 Azure Active Directory B2C (Azure AD B2C) 执行用户登录和注册。 应用程序可以使用 Azure AD B2C 通过开放式标准协议对社交帐户、企业帐户和 Azure Active Directory 帐户进行身份验证。

在本教程中，你将了解如何执行以下操作：

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

记录“应用程序(客户端) ID”，以便在后续步骤中使用  。

## <a name="configure-the-sample"></a>配置示例

在本教程中，你将配置一个可从 GitHub 下载的示例。 示例 WPF 桌面应用程序演示了如何在 Azure AD B2C 中注册、登录和调用受保护的 Web API。 从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip)、[浏览存储库](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)或克隆示例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

更新应用程序以使用 Azure AD B2C 租户并调用其用户流（而不是默认演示租户中的用户流）：

1. 在 Visual Studio 中打开“active-directory-b2c-wpf”解决方案 (`active-directory-b2c-wpf.sln`)  。
2. 在“active-directory-b2c-wpf”项目中，打开“App.xaml.cs”文件并查找以下变量定义   。 将 `{your-tenant-name}` 替换为 Azure AD B2C 租户名称，并将 `{application-ID}` 替换为之前记下的应用程序 ID。

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. 将策略名称变量更新为作为先决条件创建的用户流名称。 例如：

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>运行示例

按 **F5** 生成并运行示例。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

1. 选择“登录”以用户身份登录  。 这将使用 **B2C_1_signupsignin1** 用户流。
2. Azure AD B2C 会显示带“立即注册”链接的登录页面  。 由于你还没有帐户，因此请选择“立即注册”链接。 
3. 注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还收集用户的密码和请求的属性（在用户流中定义）。

    请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。

    ![作为登录/注册工作流的一部分显示的注册页](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. 选择“创建”，  在 Azure AD B2C 租户中创建本地帐户。

现在，用户可以使用其电子邮件地址登录并使用桌面应用程序。 成功注册或登录后，WPF 应用的下方窗格中会显示令牌详细信息。

![WPF 桌面应用程序底部窗格中显示的令牌详细信息](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

如果选择“调用 API”按钮，则会显示“错误消息”   。 出现该错误的原因是，在其当前状态下，应用程序尝试访问由演示租户 `fabrikamb2c.onmicrosoft.com` 保护的 API。 由于访问令牌仅对 Azure AD B2C 租户有效，因此 API 调用是未经授权的。

继续学习下一教程，在自己的租户中注册受保护的 Web API，并启用“调用 API”功能  。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 添加本机客户端应用程序
> * 将示例配置为使用此应用程序
> * 使用用户流进行登录

接下来，要启用“调用 API”按钮功能，请向 WPF 桌面应用程序授予访问自己 Azure AD B2C 租户所注册 Web API 的权限  ：

> [!div class="nextstepaction"]
> [教程：从桌面应用授予对 Node.js Web API 的访问权限](tutorial-desktop-app-webapi.md)
