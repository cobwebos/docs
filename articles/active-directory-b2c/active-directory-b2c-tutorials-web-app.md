---
title: 教程 - 使用 Azure Active Directory B2C 允许 Web 应用程序进行帐户身份验证 | Microsoft Docs
description: 有关如何使用 Azure Active Directory B2C 为 ASP.NET Web 应用程序提供用户登录功能的教程。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: ed34dcfb2aa488f4e7e34294b46de68624811afd
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39609032"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 允许 Web 应用程序进行帐户身份验证

本教程介绍如何使用 Azure Active Directory (Azure AD) B2C 在 ASP.NET Web 应用中进行用户登录和注册。 应用可以使用 Azure AD B2C 通过开放式标准协议对社交帐户、企业帐户和 Azure Active Directory 帐户进行身份验证。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 租户中注册示例 ASP.NET Web 应用。
> * 创建适用于用户注册、登录、配置文件编辑和密码重置的策略。
> * 将示例 Web 应用配置为使用 Azure AD B2C 租户。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 创建自己的 [Azure AD B2C 租户](active-directory-b2c-get-started.md)
* 安装带有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="register-web-app"></a>注册 Web 应用

应用程序需要先在租户中[注册](../active-directory/develop/developer-glossary.md#application-registration)，然后才能从 Azure Active Directory 接收[访问令牌](../active-directory/develop/developer-glossary.md#access-token)。 应用注册可以为租户中的应用创建一个[应用程序 ID](../active-directory/develop/developer-glossary.md#application-id-client-id)。 

以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。 现在应该使用在前一个教程中创建的租户。 

2. 在 B2C 设置中，单击“应用程序”，然后单击“添加”。 

    若要在租户中注册示例 Web 应用，请使用以下设置：

    ![添加新应用](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 我的示例 Web 应用 | 输入一个**名称**，用于向使用者描述你的应用。 | 
    | 包括 Web 应用/Web API | 是 | 对于 Web 应用，请选择“是”。 |
    | 允许隐式流 | 是 | 选择“是”，因为应用使用 [OpenID Connect 登录](active-directory-b2c-reference-oidc.md)。 |
    | 回复 URL | `https://localhost:44316` | 回复 URL 属于终结点，允许 Azure AD B2C 在其中返回应用请求的任何令牌。 在本教程中，示例在本地 (localhost) 运行，并在端口 44316 上进行侦听。 |
    | **包含本机客户端** | 否 | 由于这是 Web 应用，不是本机客户端，因此请选择“否”。 |
    
3. 单击“创建”以注册应用。

注册的应用显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择你的 Web 应用。 此时会显示 Web 应用的属性窗格。

![Web 应用程序属性](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

请记下**应用程序 ID**。 此 ID 用于唯一标识应用，是稍后在本教程中配置应用所必需的。

### <a name="create-a-client-password"></a>创建客户端密码

Azure AD B2C 对[客户端应用程序](../active-directory/develop/developer-glossary.md#client-application)使用 OAuth2 授权。 Web 应用是[机密客户端](../active-directory/develop/developer-glossary.md#web-client)，需要客户端 ID 或应用程序 ID 和客户端机密、客户端密码或应用程序密钥。

1. 选择已注册 Web 应用的“密钥”页，然后单击“生成密钥”。

2. 单击“保存”，显示应用密钥。

    ![应用常规密钥页](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

此密钥在门户中显示一次。 请务必复制并保存密钥值。 需要此值来配置应用。 将此密钥保存在安全的地方。 不要公开共享此密钥。

## <a name="create-policies"></a>创建策略

Azure AD B2C 策略定义用户工作流。 例如，登录、注册、更改密码、编辑配置文件均为常见工作流。

### <a name="create-a-sign-up-or-sign-in-policy"></a>创建注册或登录策略

若要注册用户，以便访问并登录 Web 应用，请创建**注册或登录策略**。

1. 在 Azure AD B2C 门户页中选择“注册或登录策略”，然后单击“添加”。

    若要配置策略，请使用以下设置：

    ![添加注册或登录策略](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | 输入策略的**名称**。 策略名称带 **b2c_1_** 前缀。 在示例代码中，使用完整的策略名称 **b2c_1_SiUpIn**。 | 
    | **标识提供者** | 电子邮件注册 | 用于唯一标识用户的标识提供者。 |
    | **注册属性** | 显示名称和邮政编码 | 选择要在注册期间从用户处收集的属性。 |
    | **应用程序声明** | 显示名称、邮政编码、用户为新用户、用户的对象 ID | 选择需要包括在[访问令牌](../active-directory/develop/developer-glossary.md#access-token)中的[声明](../active-directory/develop/developer-glossary.md#claim)。 |

2. 单击“创建”以创建策略。 

### <a name="create-a-profile-editing-policy"></a>创建配置文件编辑策略

若要允许用户自行重置其用户配置文件信息，请创建**配置文件编辑策略**。

1. 在 Azure AD B2C 门户页中选择“配置文件编辑策略”，然后单击“添加”。

    若要配置策略，请使用以下设置：

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiPe | 输入策略的**名称**。 策略名称带 **b2c_1_** 前缀。 在示例代码中，使用完整的策略名称 **b2c_1_SiPe**。 | 
    | **标识提供者** | 本地帐户登录名 | 用于唯一标识用户的标识提供者。 |
    | **配置文件属性** | 显示名称和邮政编码 | 选择允许用户在配置文件编辑过程中修改的属性。 |
    | **应用程序声明** | 显示名称、邮政编码、用户的对象 ID | 选择在成功地进行配置文件编辑之后，需要包括在[访问令牌](../active-directory/develop/developer-glossary.md#access-token)中的[声明](../active-directory/develop/developer-glossary.md#claim)。 |

2. 单击“创建”以创建策略。 

### <a name="create-a-password-reset-policy"></a>创建密码重置策略

若要在应用程序上启用密码重置，需要创建**密码重置策略**。 此策略描述了使用者在密码重置过程中的体验，以及应用程序在成功完成密码重置后会接收到的令牌内容。

1. 在 Azure AD B2C 门户页中选择“密码重置策略”，然后单击“添加”。

    若要配置策略，请使用以下设置。

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | 输入策略的**名称**。 策略名称带 **b2c_1_** 前缀。 在示例代码中，使用完整的策略名称 **b2c_1_SSPR**。 | 
    | **标识提供者** | 使用电子邮件地址重置密码 | 这是用于唯一标识用户的标识提供者。 |
    | **应用程序声明** | 用户的对象 ID | 选择在成功地进行密码重置之后，需要包括在[访问令牌](../active-directory/develop/developer-glossary.md#access-token)中的[声明](../active-directory/develop/developer-glossary.md#claim)。 |

2. 单击“创建”以创建策略。 

## <a name="update-web-app-code"></a>更新 Web 应用代码

注册 Web 应用并创建策略以后，需将应用配置为使用 Azure AD B2C 租户。 在本教程中，将配置一个示例 Web 应用（可从 GitHub 下载）。 

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)或克隆示例 Web 应用。 确保将示例文件提取到文件夹中时，路径的总字符长度不到 260。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

示例 ASP.NET Web 应用是一个简单的任务列表应用，用于创建和更新待办事项列表。 此应用使用 [Microsoft OWIN 中间件组件](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)，允许用户注册使用 Azure AD B2C 租户中的应用。 创建 Azure AD B2C 策略之后，用户即可使用社交帐户来访问应用，也可创建一个可用作标识的帐户来访问应用。 

示例解决方案中有两个项目：

**Web 应用示例应用 (TaskWebApp)：** 用于创建和编辑任务列表的 Web 应用。 此 Web 应用使用**注册或登录**策略来注册或登录用户。

**Web API 示例应用 (TaskService)：** 一个 Web API，支持创建、读取、更新和删除任务列表的功能。 此 Web API 受 Azure AD B2C 保护，可以通过 Web 应用进行调用。

若要在租户中使用应用注册，需更改应用，其中包括以前记录的应用程序 ID 和密钥。 此外，还需配置所创建的策略。 示例 Web 应用在 Web.config 文件中将配置值作为应用设置来定义。 若要更改应用设置，请执行以下操作：

1. 在 Visual Studio 中打开 **B2C-WebAPI-DotNet** 解决方案。

2. 在 **TaskWebApp** Web 应用项目中打开 **Web.config** 文件。 将 `ida:Tenant` 的值替换为所创建租户的名称。 将 `ida:ClientId` 的值替换为所记录的应用程序 ID。 将 `ida:ClientSecret` 的值替换为所记录的密钥。

3. 在 **Web.config** 文件中，将 `ida:SignUpSignInPolicyId` 的值替换为 `b2c_1_SiUpIn`。 将 `ida:EditProfilePolicyId` 的值替换为 `b2c_1_SiPe`。 将 `ida:ResetPasswordPolicyId` 的值替换为 `b2c_1_SSPR`。

## <a name="run-the-sample-web-app"></a>运行示例 Web 应用

在解决方案资源管理器中，右键单击 **TaskWebApp** 项目，然后单击“设为启动项目”

按 **F5** 启动 Web 应用。 默认浏览器将启动到本地网站地址 `https://localhost:44316/`。 

示例应用支持注册、登录、配置文件编辑和密码重置。 本教程重点介绍用户如何使用电子邮件地址注册，以便使用应用。 可以自行探索其他方案。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

1. 单击顶部横幅中的“注册/登录”链接，以 Web 应用用户身份注册。 这样就可以使用在前面的步骤中定义的 **b2c_1_SiUpIn** 策略。

2. Azure AD B2C 会显示带注册链接的登录页面。 由于你还没有帐户，因此请单击“立即注册”链接。 

3. 注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还收集用户的密码和请求的属性（在策略中定义）。

    请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。 

    ![注册工作流](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. 单击“创建”，在 Azure AD B2C 租户中创建本地帐户。

现在，用户可以使用其电子邮件地址登录并使用 Web 应用了。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure AD B2C 租户、如何创建策略，以及如何更新示例 Web 应用，以便使用 Azure AD B2C 租户。 若要了解如何注册、配置和调用受 Azure AD B2C 租户保护的 ASP.NET Web API，请继续完成下一教程。

> [!div class="nextstepaction"]
> [教程：使用 Azure Active Directory B2C 保护 ASP.NET Web API](active-directory-b2c-tutorials-web-api.md)
