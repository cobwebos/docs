---
title: 教程 - 使单页应用能够使用帐户通过 Azure Active Directory B2C 进行身份验证 | Microsoft Docs
description: 有关如何使用 Azure Active Directory B2C 为单页应用程序 (JavaScript) 提供用户登录功能的教程。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: cce76a0e97e039ec6e6c3a976d1fc7caca7fde73
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834428"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>教程：使单页应用能够使用帐户通过 Azure Active Directory B2C 进行身份验证

本教程展示了如何在单页应用程序 (SPA) 中使用 Azure Active Directory (Azure AD) B2C 执行用户登录和注册。 应用可以使用 Azure AD B2C 通过开放式标准协议对社交帐户、企业帐户和 Azure Active Directory 帐户进行身份验证。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 目录中注册示例单页应用程序。
> * 创建适用于用户注册、登录、配置文件编辑和密码重置的用户流。
> * 将示例应用程序配置为使用 Azure AD B2C 目录。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 创建自己的 [Azure AD B2C 目录](active-directory-b2c-get-started.md)
* 安装带有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 或更高版本
* 安装 [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>注册单页应用

应用程序需要先在目录中[注册](../active-directory/develop/developer-glossary.md#application-registration)，然后才能从 Azure Active Directory 接收[访问令牌](../active-directory/develop/developer-glossary.md#access-token)。 应用注册可以为目录中的应用创建一个[应用程序 ID](../active-directory/develop/developer-glossary.md#application-id-client-id)。 

以 Azure AD B2C 目录的全局管理员的身份登录 [Azure 门户](https://portal.azure.com/)。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. 从 Azure 门户的服务列表中选择“Azure AD B2C”。 

2. 在 B2C 设置中，单击“应用程序”，然后单击“添加”。 

    若要在目录中注册示例 Web 应用，请使用以下设置：
    
    ![添加新应用](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名称** | 我的示例单页应用 | 输入一个**名称**，用于向使用者描述你的应用。 | 
    | 包括 Web 应用/Web API | 是 | 对于是否为单页应用，选择“是”。 |
    | 允许隐式流 | 是 | 选择“是”，因为应用使用 [OpenID Connect 登录](active-directory-b2c-reference-oidc.md)。 |
    | 回复 URL | `http://localhost:6420` | 回复 URL 属于终结点，允许 Azure AD B2C 在其中返回应用请求的任何令牌。 在本教程中，示例在本地 (localhost) 运行，并在端口 6420 上进行侦听。 |
    | **包含本机客户端** | 否 | 由于这是一个单页应用，不是本机客户端，因此请选择“否”。 |
    
3. 单击“创建”以注册应用。

注册的应用显示在 Azure AD B2C 目录的应用程序列表中。 从列表中选择你的单页应用。 此时将显示已注册的单页应用的属性窗格。

![单页应用属性](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

请记下“应用程序客户端 ID”。 此 ID 用于唯一标识应用，是稍后在本教程中配置应用所必需的。

## <a name="create-user-flows"></a>创建用户流

Azure AD B2C 用户流定义标识任务的用户体验。 例如，登录、注册、更改密码、编辑配置文件均为常见用户流。

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>创建注册或登录用户流

若要注册用户，以便访问并登录 Web 应用，请创建**注册或登录用户流**。

1. 在 Azure AD B2C 门户页中选择“用户流”，然后单击“新建用户流”。
2. 在“建议”选项卡上，单击“注册和登录”。

    若要配置用户流，请使用以下设置：

    ![添加注册或登录用户流](media/active-directory-b2c-tutorials-spa/add-susi-user-flow.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名称** | SiUpIn | 输入该用户流的**名称**。 用户流名称以 **B2C_1_** 为前缀。 示例代码中使用了完整的用户流名称 **B2C_1_SiUpIn**。 | 
    | **标识提供者** | 电子邮件注册 | 用于唯一标识用户的标识提供者。 |

3. 在“用户特性和声明”下单击“显示更多”，然后选择以下设置：

    ![添加注册或登录用户流](media/active-directory-b2c-tutorials-spa/add-attributes-and-claims.png)

    | 列      | 建议的值  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **收集特性** | 显示名称和邮政编码 | 选择要在注册期间从用户处收集的属性。 |
    | **返回声明** | 显示名称、邮政编码、用户为新用户、用户的对象 ID | 选择需要包括在[访问令牌](../active-directory/develop/developer-glossary.md#access-token)中的[声明](../active-directory/develop/developer-glossary.md#claim)。 |

4. 单击“确定”。
5. 单击“创建”以创建用户流。 

### <a name="create-a-profile-editing-user-flow"></a>创建配置文件编辑用户流

若要允许用户自行重置其用户配置文件信息，请创建**配置文件编辑用户流**。

1. 在 Azure AD B2C 门户页中选择“用户流”，然后单击“新建用户流”。
2. 在“建议”选项卡上，单击“配置文件编辑”。

    若要配置用户流，请使用以下设置：

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名称** | SiPe | 输入该用户流的**名称**。 用户流名称以 **B2C_1_** 为前缀。 示例代码中使用了完整的用户流名称 **B2C_1_SiPe**。 | 
    | **标识提供者** | 本地帐户登录名 | 用于唯一标识用户的标识提供者。 |

3.  在“用户特性”下单击“显示更多”，然后选择以下设置：

    | 列      | 建议的值  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **收集特性** | 显示名称和邮政编码 | 选择允许用户在配置文件编辑过程中修改的属性。 |
    | **返回声明** | 显示名称、邮政编码、用户的对象 ID | 选择在成功地进行配置文件编辑之后，需要包括在[访问令牌](../active-directory/develop/developer-glossary.md#access-token)中的[声明](../active-directory/develop/developer-glossary.md#claim)。 |

4. 单击“确定”。
5. 单击“创建”以创建用户流。 

### <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

若要在应用程序上启用密码重置，需要创建**密码重置用户流**。 此用户流描述了使用者在密码重置过程中的体验，以及应用程序在成功完成密码重置后会接收到的令牌内容。

1. 在 Azure AD B2C 门户页中选择“用户流”，然后单击“新建用户流”。
2. 在“建议”选项卡上，单击“密码重置”。

    若要配置用户流，请使用以下设置。

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名称** | SSPR | 输入该用户流的**名称**。 用户流名称以 **B2C_1_** 为前缀。 示例代码中使用了完整的用户流名称 **B2C_1_SSPR**。 | 
    | **标识提供者** | 使用电子邮件地址重置密码 | 这是用于唯一标识用户的标识提供者。 |

3. 在“应用程序声明”下单击“显示更多”，然后选择以下设置：

    | 列      | 建议的值  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **返回声明** | 用户的对象 ID | 选择在成功地进行密码重置之后，需要包括在[访问令牌](../active-directory/develop/developer-glossary.md#access-token)中的[声明](../active-directory/develop/developer-glossary.md#claim)。 |

4. 单击“确定”。
5. 单击“创建”以创建用户流。 

## <a name="update-single-page-app-code"></a>更新单页应用代码

现在，你已注册了一个应用并创建了用户流，接下来需要将该应用配置为使用你的 Azure AD B2C 目录。 在本教程中，将配置一个可从 GitHub 下载的示例 SPA JavaScript 应用。 

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)或克隆示例 Web 应用。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
该示例应用演示了单页应用可以如何使用 Azure AD B2C 执行用户注册、登录，以及如何调用受保护的 Web API。 需要更改应用，以便使用目录中的应用注册并配置已创建的用户流。 

若要更改应用设置，请执行以下操作：

1. 打开 Node.js 单页应用示例中的 `index.html` 文件。
2. 使用 Azure AD B2C 目录注册信息来配置示例。 更改以下代码行（确保将值替换为你的目录和 API 的名称）：

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    在本教程中使用的用户流名称为 **B2C_1_SiUpIn**。 如果你使用了不同的用户流名称，请在 `authority` 值中使用你的用户流名称。

## <a name="run-the-sample"></a>运行示例

1. 启动 Node.js 命令提示符。
2. 切换到包含 Node.js 示例的目录。 例如 `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. 运行以下命令：
    ```
    npm install && npm update
    node server.js
    ```

    控制台窗口将显示承载着该应用的位置的端口号。
    
    ```
    Listening on port 6420...
    ```

4. 使用浏览器导航到地址 `http://localhost:6420` 来查看该应用。

示例应用支持注册、登录、配置文件编辑和密码重置。 本教程重点介绍用户如何使用电子邮件地址注册，以便使用应用。 可以自行探索其他方案。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

1. 单击“登录”来以 SPA 应用用户的身份登录。 这将使用在前面的步骤中定义的 **B2C_1_SiUpIn** 用户流。

2. Azure AD B2C 会显示带注册链接的登录页面。 由于你还没有帐户，因此请单击“立即注册”链接。 

3. 注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还收集用户的密码和请求的属性（在用户流中定义）。

    请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。 

    ![注册工作流](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. 单击“创建”，在 Azure AD B2C 目录中创建本地帐户。

现在，用户可以使用其电子邮件地址登录并使用 SPA 应用了。

> [!NOTE]
> 登录后，应用会显示“权限不足”错误。 收到此错误是因为你正在尝试从演示目录访问资源。 因为你的访问令牌仅对你的 Azure AD 目录有效，所以该 API 调用未经授权。 请继续学习下一教程，了解如何为目录创建受保护的 Web API。 

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 教程，可以使用 Azure AD B2C 目录。 可以在不再需要时[删除 Azure AD B2C 目录](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure AD B2C 目录、如何创建用户流，以及如何更新示例单页应用来使用 Azure AD B2C 目录。 请继续学习下一教程来学习如何从桌面应用注册、配置和调用受保护的 Web API。

> [!div class="nextstepaction"]
> [Azure AD B2C 代码示例](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
