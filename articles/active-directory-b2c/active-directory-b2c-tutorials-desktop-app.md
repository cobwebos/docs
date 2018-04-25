---
title: 教程 - 使桌面应用能够使用帐户通过 Azure Active Directory B2C 进行身份验证 | Microsoft Docs
description: 有关如何使用 Azure Active Directory B2C 为 .NET 桌面应用提供用户登录功能的教程。
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: parja
ms.date: 2/28/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 86d8b105828bdb2a83eac24aebf227b9ae7615e2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>教程：使桌面应用能够使用帐户通过 Azure Active Directory B2C 进行身份验证

本教程展示了如何在 Windows Presentation Foundation (WPF) 桌面应用程序中使用 Azure Active Directory (Azure AD) B2C 执行用户登录和注册。 应用可以使用 Azure AD B2C 通过开放式标准协议对社交帐户、企业帐户和 Azure Active Directory 帐户进行身份验证。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 租户中注册示例桌面应用。
> * 创建适用于用户注册、登录、配置文件编辑和密码重置的策略。
> * 将示例应用程序配置为使用你的 Azure AD B2C 租户。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 创建自己的 [Azure AD B2C 租户](active-directory-b2c-get-started.md)
* 安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)，其中包含 **.NET 桌面开发**与 **ASP.NET 和 Web 开发**工作负荷。

## <a name="register-desktop-app"></a>注册桌面应用

应用程序需要先在租户中[注册](../active-directory/develop/active-directory-dev-glossary.md#application-registration)，然后才能从 Azure Active Directory 接收[访问令牌](../active-directory/develop/active-directory-dev-glossary.md#access-token)。 应用注册可以为租户中的应用创建一个[应用程序 ID](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id)。 

以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. 从 Azure 门户的服务列表中选择“Azure AD B2C”。 

2. 在 B2C 设置中，单击“应用程序”，然后单击“添加”。 

    若要在租户中注册示例 Web 应用，请使用以下设置：
    
    ![添加新应用](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 我的示例 WPF 应用 | 输入一个**名称**，用于向使用者描述你的应用。 | 
    | 包括 Web 应用/Web API | 否 | 对于是否为桌面应用，选择“否”。 |
    | **包含本机客户端** | 是 | 因为这是一个桌面应用并且被视为本机客户端。 |
    | **重定向 URI** | 默认值 | Azure AD B2C 要在 OAuth 2.0 响应中将用户代理重定向到的唯一标识符。 |
    | 自定义重定向 URI | `com.onmicrosoft.contoso.appname://redirect/path` | 输入 `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path` 策略会将令牌发送到此 URI。 |
    
3. 单击“创建”以注册应用。

注册的应用显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择你的桌面应用。 此时将显示已注册的桌面应用的属性窗格。

![桌面应用属性](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

请记下“应用程序客户端 ID”。 此 ID 用于唯一标识应用，是稍后在本教程中配置应用所必需的。

## <a name="create-policies"></a>创建策略

Azure AD B2C 策略定义用户工作流。 例如，登录、注册、更改密码、编辑配置文件均为常见工作流。

### <a name="create-a-sign-up-or-sign-in-policy"></a>创建注册或登录策略

若要注册用户，以便访问并登录桌面应用，请创建**注册或登录策略**。

1. 在 Azure AD B2C 门户页中选择“注册或登录策略”，然后单击“添加”。

    若要配置策略，请使用以下设置：

    ![添加注册或登录策略](media/active-directory-b2c-tutorials-desktop-app/add-susi-policy.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | 输入策略的**名称**。 策略名称以 **B2C_1_** 为前缀。 示例代码中使用了完整的策略名称 **B2C_1_SiUpIn**。 | 
    | **标识提供者** | 电子邮件注册 | 用于唯一标识用户的标识提供者。 |
    | **注册属性** | 显示名称和邮政编码 | 选择要在注册期间从用户处收集的属性。 |
    | **应用程序声明** | 显示名称、邮政编码、用户为新用户、用户的对象 ID | 选择需要包括在[访问令牌](../active-directory/develop/active-directory-dev-glossary.md#access-token)中的[声明](../active-directory/develop/active-directory-dev-glossary.md#claim)。 |

2. 单击“创建”以创建策略。 

### <a name="create-a-profile-editing-policy"></a>创建配置文件编辑策略

若要允许用户自行重置其用户配置文件信息，请创建**配置文件编辑策略**。

1. 在 Azure AD B2C 门户页中选择“配置文件编辑策略”，然后单击“添加”。

    若要配置策略，请使用以下设置：

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiPe | 输入策略的**名称**。 策略名称以 **B2C_1_** 为前缀。 示例代码中使用了完整的策略名称 **B2C_1_SiPe**。 | 
    | **标识提供者** | 本地帐户登录名 | 用于唯一标识用户的标识提供者。 |
    | **配置文件属性** | 显示名称和邮政编码 | 选择允许用户在配置文件编辑过程中修改的属性。 |
    | **应用程序声明** | 显示名称、邮政编码、用户的对象 ID | 选择在成功地进行配置文件编辑之后，需要包括在[访问令牌](../active-directory/develop/active-directory-dev-glossary.md#access-token)中的[声明](../active-directory/develop/active-directory-dev-glossary.md#claim)。 |

2. 单击“创建”以创建策略。 

### <a name="create-a-password-reset-policy"></a>创建密码重置策略

若要在应用程序上启用密码重置，需要创建**密码重置策略**。 此策略描述了使用者在密码重置过程中的体验，以及应用程序在成功完成密码重置后会接收到的令牌内容。

1. 在 Azure AD B2C 门户页中选择“密码重置策略”，然后单击“添加”。

    若要配置策略，请使用以下设置。

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | 输入策略的**名称**。 策略名称以 **B2C_1_** 为前缀。 示例代码中使用了完整的策略名称 **B2C_1_SSPR**。 | 
    | **标识提供者** | 使用电子邮件地址重置密码 | 这是用于唯一标识用户的标识提供者。 |
    | **应用程序声明** | 用户的对象 ID | 选择在成功地进行密码重置之后，需要包括在[访问令牌](../active-directory/develop/active-directory-dev-glossary.md#access-token)中的[声明](../active-directory/develop/active-directory-dev-glossary.md#claim)。 |

2. 单击“创建”以创建策略。 

## <a name="update-desktop-app-code"></a>更新桌面应用代码

现在，你已注册了一个桌面应用并创建了策略，接下来需要将该应用配置为使用你的 Azure AD B2C 租户。 在本教程中，将配置一个示例桌面应用。 

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)或克隆该示例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

该示例 WPF 桌面应用演示了桌面应用可以如何使用 Azure AD B2C 执行用户注册、登录，以及如何调用受保护的 Web API。

你需要更改应用来使用你的租户中的应用注册并配置您创建的策略。 

若要更改应用设置，请执行以下操作：

1. 在 Visual Studio 中打开 `active-directory-b2c-wpf` 解决方案。

2. 在 `active-directory-b2c-wpf` 项目中，打开 **App.xaml.cs** 文件并进行以下更新：

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. 将 **PolicySignUpSignIn** 变量更新为在前面的步骤中创建的*注册或登录策略*名称。 请记住要包含 *B2C_1_* 前缀。

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>运行示例桌面应用程序

按 **F5** 生成并运行桌面应用。 

示例应用支持注册、登录、配置文件编辑和密码重置。 本教程重点介绍用户如何使用电子邮件地址注册，以便使用应用。 可以自行探索其他方案。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

1. 单击“登录”按钮来以桌面应用用户的身份进行登录。 这将使用在前面的步骤中定义的 **B2C_1_SiUpIn** 策略。

2. Azure AD B2C 会显示带注册链接的登录页面。 由于你还没有帐户，因此请单击“立即注册”链接。 

3. 注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还收集用户的密码和请求的属性（在策略中定义）。

    请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。 

    ![注册工作流](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. 单击“创建”，在 Azure AD B2C 租户中创建本地帐户。

现在，用户可以使用其电子邮件地址登录并使用桌面应用了。

> [!NOTE]
> 如果单击“调用 API”按钮，将会收到“未经授权”错误。 收到此错误是因为你正在尝试从演示租户访问资源。 因为你的访问令牌仅对你的 Azure AD 租户有效，所以该 API 调用未经授权。 请继续学习下一教程来为你的租户创建受保护的 Web API。 

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何创建 Azure AD B2C 租户、如何创建策略，以及如何更新示例桌面应用来使用你的 Azure AD B2C 租户。 请继续学习下一教程来学习如何从桌面应用注册、配置和调用受保护的 Web API。

> [!div class="nextstepaction"]
> 