---
title: 教程 - 在单页应用程序中启用身份验证 - Azure Active Directory B2C
description: 了解如何使用 Azure Active Directory B2C 为单页应用程序 (JavaScript) 提供用户登录功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/08/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 496cf801a44638af61306b43791abce9466e2cb2
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835685"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 在单页应用程序中启用身份验证

本教程展示了如何在单页应用程序 (SPA) 中使用 Azure Active Directory (Azure AD) B2C 执行用户登录和注册。 应用程序可以使用 Azure AD B2C 通过开放式标准协议对社交帐户、企业帐户和 Azure Active Directory 帐户进行身份验证。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新此应用程序
> * 将示例配置为使用此应用程序
> * 使用用户流进行登录

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

在继续执行本教程中的步骤以前，需将以下 Azure AD B2C 资源准备到位：

* [Azure AD B2C 租户](tutorial-create-tenant.md)
* 在租户中[注册的应用程序](tutorial-register-applications.md)
* 在租户中[创建的用户流](tutorial-create-user-flows.md)

另外，需要在本地开发环境中备好以下项：

* 代码编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/) 或 [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.0.0](https://www.microsoft.com/net/core) 或更高版本
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>更新应用程序

在按照先决条件完成的第二个教程中，你已在 Azure AD B2C 中注册了 Web 应用程序。 若要与教程中的示例通信，需添加一个指向 Azure AD B2C 中的应用程序的重定向 URI。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
1. 选择“应用程序”，然后选择“webapp1”应用程序。  
1. 在“回复 URL”下添加 `http://localhost:6420`。 
1. 选择“保存”。 
1. 在属性页上记录“应用程序 ID”。  在后面的步骤中，当你在单页 Web 应用程序中更新代码时，需使用此应用 ID。

## <a name="get-the-sample-code"></a>获取示例代码

在本教程中，我们配置一个从 GitHub 下载的代码示例。 该示例演示单页应用程序可以如何使用 Azure AD B2C 执行用户注册和登录，以及如何调用受保护的 Web API。

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)或克隆该示例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>更新示例

获得示例以后，即可使用在此前的步骤中记录的 Azure AD B2C 租户名称和应用程序 ID 来更新代码。

1. 打开示例目录的根中的 `index.html` 文件。
1. 在 `msalConfig` 定义中，使用在以前的步骤中记录的应用程序 ID 修改 **clientId** 值。 接下来，使用 Azure AD B2C 租户名称更新 **authority** URI 值。 另外，使用在某个先决条件中创建的注册/登录用户流的名称（例如 *B2C_1_signupsignin1*）更新 URI。

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    在本教程中使用的用户流的名称为 **B2C_1_signupsignin1**。 如果使用的是其他用户流名称，请在 `authority` 值中指定该名称。

## <a name="run-the-sample"></a>运行示例

1. 打开控制台窗口，切换到包含此示例的目录。 例如：

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. 运行以下命令：

    ```
    npm install && npm update
    node server.js
    ```

    控制台窗口显示本地运行的 Node.js 服务器的端口号：

    ```
    Listening on port 6420...
    ```

1. 在浏览器中导航到 `http://localhost:6420`，查看此应用程序。

示例支持注册、登录、配置文件编辑和密码重置。 本教程重点介绍用户如何使用电子邮件地址注册。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

1. 单击“登录”  ，注册为应用程序用户。 这将使用在前面的步骤中指定的 **B2C_1_signupsignin1** 用户流。
1. Azure AD B2C 会显示带注册链接的登录页面。 由于你还没有帐户，因此请单击“立即注册”链接。 
1. 注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还收集用户的密码和请求的属性（在用户流中定义）。

    请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。

    ![登录/注册用户流提供的注册页](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. 单击“创建”，  在 Azure AD B2C 目录中创建本地帐户。

单击“创建”时，注册页关闭，登录页重新显示。 

现在可以使用电子邮件地址和密码登录到应用程序。

### <a name="error-insufficient-permissions"></a>错误: 权限不足

登录后，应用显示“权限不足”错误 - 这是**预期行为**：

`ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.`

收到此错误是因为你正在尝试从演示目录访问资源，但你的访问令牌仅适用于 Azure AD 目录。 因此，该 API 调用未经授权。

请继续学习此系列中的下一教程（参见[后续步骤](#next-steps)），了解如何为目录创建受保护的 Web API。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新此应用程序
> * 将示例配置为使用此应用程序
> * 使用用户流进行登录

现在可转到此系列的下一教程，了解如何授予用户访问权限，以便从 SPA 访问受保护的 Web API：

> [!div class="nextstepaction"]
> [教程：从单页应用使用 Azure Active Directory B2C 授予对 ASP.NET Core Web API 的访问权限](active-directory-b2c-tutorials-spa-webapi.md)
