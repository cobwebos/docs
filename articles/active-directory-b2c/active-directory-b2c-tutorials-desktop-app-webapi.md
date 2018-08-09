---
title: 教程 - 从桌面应用使用 Azure Active Directory B2C 授予对 Node.js Web API 的访问权限 | Microsoft Docs
description: 有关如何从 .NET 桌面应用使用 Active Directory B2C 保护 .Node.js Web API 并调用该 API 的教程。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 98c86f5613116dce5423aa9ca6a2ff43e5414592
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594774"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>教程：从桌面应用使用 Azure Active Directory B2C 授予对 Node.js Web API 的访问权限

本教程展示了如何从 Windows Presentation Foundation (WPF) 桌面应用调用受 Azure Active Directory (Azure AD) B2C 保护的 Node.js Web API 资源。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 租户中注册 Web API
> * 定义并配置 Web API 的作用域
> * 授予应用访问 Web API 的权限
> * 更新示例代码，以便使用 Azure AD B2C 来保护 Web API

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完成[在桌面应用中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-desktop-app.md)教程。
* 安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)，其中包含 **.NET 桌面开发**与 **ASP.NET 和 Web 开发**工作负荷。
* 安装 [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>注册 Web API

Web API 资源需要先在租户中注册，然后才能接受并响应通过 Azure Active Directory 提供[访问令牌](../active-directory/develop/developer-glossary.md#access-token)的[客户端应用程序](../active-directory/develop/developer-glossary.md#client-application)所提出的[受保护资源请求](../active-directory/develop/developer-glossary.md#resource-server)。 注册时，会在租户中构建[应用程序和服务主体对象](../active-directory/develop/developer-glossary.md#application-object)。 

以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. 从 Azure 门户的服务列表中选择“Azure AD B2C”。

2. 在 B2C 设置中，单击“应用程序”，然后单击“添加”。

    若要在租户中注册示例 Web API，请使用以下设置。
    
    ![添加新 API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 我的示例 Node.js Web API | 输入一个**名称**，描述面向开发人员的 Web API。 |
    | 包括 Web 应用/Web API | 是 | 对于 Web API，请选择“是”。 |
    | 允许隐式流 | 是 | 选择“是”，因为 API 使用 [OpenID Connect 登录](active-directory-b2c-reference-oidc.md)。 |
    | 回复 URL | `http://localhost:5000` | 回复 URL 属于终结点，允许 Azure AD B2C 在其中返回 API 请求的任何令牌。 在本教程中，示例 Web API 在本地 (localhost) 运行，并在端口 5000 上进行侦听。 |
    | 应用 ID URI | demoapi | 此 URI 可唯一标识租户中的 API。 这样即可每个租户注册多个 API。 [作用域](../active-directory/develop/developer-glossary.md#scopes)控制对受保护 API 资源的访问，是按 App ID URI 来定义的。 |
    | 本机客户端 | 否 | 由于这是 Web API，不是本机客户端，因此请选择“否”。 |
    
3. 单击“创建”以注册 API。

注册的 API 显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择 Web API。 此时会显示 Web API 的属性窗格。

![Web API 属性](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

请记下“应用程序客户端 ID”。 此 ID 用于唯一标识 API，是稍后在本教程中配置 API 所必需的。

通过 Azure AD B2C 注册 Web API 即可定义一种信任关系。 此 API 是通过 B2C 注册的，因此现在可以信任从其他应用程序收到的 B2C 访问令牌。

## <a name="define-and-configure-scopes"></a>定义并配置作用域

可以通过[作用域](../active-directory/develop/developer-glossary.md#scopes)控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让某些用户拥有读取和写入访问权限，让另一些用户拥有只读权限。 在本教程中，请为 Web API 定义读取和写入权限。

### <a name="define-scopes-for-the-web-api"></a>定义 Web API 的作用域

注册的 API 显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择 Web API。 此时会显示 Web API 的属性窗格。

单击“发布的作用域(预览)”。

若要配置 API 的作用域，请添加以下条目。 

![在 Web API 中定义的作用域](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| 设置      | 建议的值  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **范围** | demo.read | 对 demo API 的读取访问权限|

单击“ **保存**”。

可以使用发布的作用域向客户端应用授予对 Web API 的权限。

### <a name="grant-app-permissions-to-web-api"></a>授予应用访问 Web API 的权限

若要从应用调用受保护的 Web API，需授予应用访问该 API 的权限。 在本教程中，请使用在[在桌面应用中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-desktop-app.md)教程中创建的桌面应用。

1. 从 Azure 门户的服务列表中选择“Azure AD B2C”，然后单击“应用程序”，查看已注册应用的列表。

2. 从应用列表中选择“我的示例 WPF 应用”，单击“API 访问权限(预览版)”，然后单击“添加”。

3. 在“选择 API”下拉列表中，选择已注册的 Web API“我的示例 Node.js Web API”。

4. 在“选择作用域”下拉列表中，选择在 Web API 注册中定义的作用域。

    ![选择应用的作用域](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. 单击“确定”。

这将注册**我的示例 WPF 应用**，以便调用受保护的**我的示例 Node.js Web API**。 用户通过 Azure AD B2C 进行[身份验证](../active-directory/develop/developer-glossary.md#authentication)，以便使用 WPF 桌面应用。 桌面应用从 Azure AD B2C 获取[授权](../active-directory/develop/developer-glossary.md#authorization-grant)，以便访问受保护的 Web API。

## <a name="update-web-api-code"></a>更新 Web API 代码

注册 Web API 并定义作用域以后，需配置 Web API 代码，以便使用 Azure AD B2C 租户。 在本教程中，将配置一个可从 GitHub 下载的示例 Node.js Web 应用。 

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)或克隆示例 Web 应用。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
该 Node.js Web API 示例使用 Passport.js 库来使得 Azure AD B2C 能够保护对 API 的调用。 

### <a name="configure-the-web-api"></a>配置 Web API

1. 打开 Node.js Web API 示例中的 `index.html` 文件。
2. 使用 Azure AD B2C 租户注册信息来配置示例。 更改以下代码行：

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>配置桌面应用

1. 在 Visual Studio 中打开[在桌面应用中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-desktop-app.md)教程中创建的 `active-directory-b2c-wpf` 解决方案。

## <a name="run-the-sample"></a>运行示例

运行 Node.js Web API：

1. 启动 Node.js 命令提示符。
2. 切换到包含 Node.js 示例的目录。 例如 `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 运行以下命令：
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
运行桌面应用：

1. 按 **F5** 来运行桌面应用。
2. 使用[在桌面应用中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-desktop-app.md)教程中使用的电子邮件地址和密码进行登录。
3. 单击“调用 API”按钮。 

桌面应用将向 Web API 发出请求并得到其中包含已登录用户的显示名称的响应。 受保护的桌面应用调用的是 Azure AD B2C 租户中的受保护 Web API。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本文详细介绍了如何通过在 Azure AD B2C 中注册和定义作用域来保护 ASP.NET Web API。 接下来请通过浏览可用的 Azure AD B2C 代码示例来了解更多内容。

> [!div class="nextstepaction"]
> [Azure AD B2C 代码示例](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
