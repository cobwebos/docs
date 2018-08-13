---
title: 教程 - 使用 Azure Active Directory B2C 通过 Web 应用授予对 ASP.NET Web API 的访问权限 | Microsoft Docs
description: 有关如何使用 Active Directory B2C 保护 ASP.NET Web API 并通过 ASP.NET Web 应用对其进行调用的教程。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 01/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 469a3662b5bc4db467dde3285d557ac8bbae368e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39609083"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-from-a-web-app-using-azure-active-directory-b2c"></a>教程：使用 Azure Active Directory B2C 通过 Web 应用授予对 ASP.NET Web API 的访问权限

本教程介绍如何从 ASP.NET Web 应用调用受 Azure Active Directory (Azure AD) B2C 保护的 Web API 资源。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure AD B2C 租户中注册 Web API
> * 定义并配置 Web API 的作用域
> * 授予应用访问 Web API 的权限
> * 更新示例代码，以便使用 Azure AD B2C 来保护 Web API

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 完成[“使用 Azure Active Directory B2C 在 ASP.NET Web 应用中进行用户身份验证”教程](active-directory-b2c-tutorials-web-app.md)。
* 安装带有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="register-web-api"></a>注册 Web API

Web API 资源需要先在租户中注册，然后才能接受并响应通过 Azure Active Directory 提供[访问令牌](../active-directory/develop/developer-glossary.md#access-token)的[客户端应用程序](../active-directory/develop/developer-glossary.md#client-application)所提出的[受保护资源请求](../active-directory/develop/developer-glossary.md#resource-server)。 注册时，会在租户中构建[应用程序和服务主体对象](../active-directory/develop/developer-glossary.md#application-object)。 

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。

    ![切换目录](./media/active-directory-b2c-tutorials-web-api/switch-directories.png)

3. 选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-tutorials-web-api/select-directory.png)

4. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。 现在应该使用在前一个教程中创建的租户。

5. 选择“应用程序”，然后选择“添加”。

    若要在租户中注册示例 Web API，请使用以下设置。
    
    ![添加新 API](./media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 我的示例 Web API | 输入一个**名称**，描述面向开发人员的 Web API。 |
    | 包括 Web 应用/Web API | 是 | 对于 Web API，请选择“是”。 |
    | 允许隐式流 | 是 | 选择“是”，因为 API 使用 [OpenID Connect 登录](active-directory-b2c-reference-oidc.md)。 |
    | 回复 URL | `https://localhost:44332` | 回复 URL 属于终结点，允许 Azure AD B2C 在其中返回 API 请求的任何令牌。 在本教程中，示例 Web API 在本地 (localhost) 运行，并在端口 44332 上进行侦听。 |
    | 应用 ID URI | myAPISample | 此 URI 可唯一标识租户中的 API。 这样即可每个租户注册多个 API。 [作用域](../active-directory/develop/developer-glossary.md#scopes)控制对受保护 API 资源的访问，是按 App ID URI 来定义的。 |
    | 本机客户端 | 否 | 由于这是 Web API，不是本机客户端，因此请选择“否”。 |
    
6. 单击“创建”以注册 API。

注册的 API 显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择 Web API。 此时会显示 Web API 的属性窗格。

![Web API 属性](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

请记下“应用程序客户端 ID”。 此 ID 用于唯一标识 API，是稍后在本教程中配置 API 所必需的。

通过 Azure AD B2C 注册 Web API 即可定义一种信任关系。 此 API 是通过 B2C 注册的，因此现在可以信任从其他应用程序收到的 B2C 访问令牌。

## <a name="define-and-configure-scopes"></a>定义并配置作用域

可以通过[作用域](../active-directory/develop/developer-glossary.md#scopes)控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让 Web API 用户拥有读取和写入访问权限，或者只拥有读取访问权限。 在本教程中，请使用作用域为 Web API 定义读取和写入权限。

### <a name="define-scopes-for-the-web-api"></a>定义 Web API 的作用域

注册的 API 显示在 Azure AD B2C 租户的应用程序列表中。 从列表中选择 Web API。 此时会显示 Web API 的属性窗格。

单击“发布的作用域(预览)”。

若要配置 API 的作用域，请添加以下条目。 

![在 Web API 中定义的作用域](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| 设置      | 建议的值  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **范围** | Hello.Read | 对 hello 的读取访问权限 |
| **范围** | Hello.Write | 对 hello 的写入访问权限 |

单击“ **保存**”。

可以使用发布的作用域向客户端应用授予对 Web API 的权限。

### <a name="grant-app-permissions-to-web-api"></a>授予应用访问 Web API 的权限

若要从应用调用受保护的 Web API，需授予应用访问该 API 的权限。 在本教程中，将使用在[“使用 Azure Active Directory B2C 在 ASP.NET Web 应用中进行用户身份验证”教程](active-directory-b2c-tutorials-web-app.md)中创建的 Web 应用。 

1. 从 Azure 门户的服务列表中选择“Azure AD B2C”，然后单击“应用程序”，查看已注册应用的列表。

2. 从应用列表中选择“我的示例 Web 应用”，单击“API 访问权限(预览)”，然后单击“添加”。

3. 在“选择 API”下拉列表中，选择注册的 Web API“我的示例 Web API”。

4. 在“选择作用域”下拉列表中，选择在 Web API 注册中定义的作用域。

    ![选择应用的作用域](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. 单击“确定”。

“我的示例 Web 应用”已注册，可以调用受保护的“我的示例 Web API”了。 用户通过 Azure AD B2C 进行[身份验证](../active-directory/develop/developer-glossary.md#authentication)，以便使用 Web 应用。 Web 应用从 Azure AD B2C 获取[授权](../active-directory/develop/developer-glossary.md#authorization-grant)，以便访问受保护的 Web API。

## <a name="update-code"></a>更新代码

注册 Web API 并定义作用域以后，需配置 Web API 代码，以便使用 Azure AD B2C 租户。 在本教程中，可以配置一个示例 Web API。 

示例 Web API 包括在在以下先决条件教程中下载的项目中：[“使用 Azure Active Directory B2C 在 ASP.NET Web 应用中进行用户身份验证”教程](active-directory-b2c-tutorials-web-app.md)。 如果尚未完成先决条件教程，请先完成该教程，然后再继续。

示例解决方案中有两个项目：

**Web 应用示例应用 (TaskWebApp)：** 用于创建和编辑任务列表的 Web 应用。 此 Web 应用使用**注册或登录**策略，通过电子邮件地址注册或登录用户。

**Web API 示例应用 (TaskService)：** 一个 Web API，支持创建、读取、更新和删除任务列表的功能。 此 Web API 受 Azure AD B2C 保护，可以通过 Web 应用进行调用。

示例 Web 应用和 Web API 在每个项目的 Web.config 文件中将配置值作为应用设置来定义。

在 Visual Studio 中打开 **B2C-WebAPI-DotNet** 解决方案。

### <a name="configure-the-web-app"></a>配置 Web 应用

1. 打开 **TaskWebApp** 项目中的 **Web.config**。

2. 若要在本地运行 API，请使用针对 **api:TaskServiceUrl** 的 localhost 设置。 更改 Web.config，如下所示： 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. 配置 API 的 URI。 这是 Web 应用用来发起 API 请求的 URI。 另请配置所请求的权限。

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>配置 Web API

1. 打开 **TaskService** 项目中的 **Web.config**。

2. 配置要使用租户的 API。

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. 将客户端 ID 设置为 API 的注册应用程序 ID。

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. 使用在创建注册和登录策略时生成的名称更新策略设置。

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_SiUpIn" />
    ```

5. 配置作用域设置，使之与门户中创建的设置相匹配。

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>运行示例

**TaskWebApp** 和 **TaskService** 项目都需要运行。 

1. 在解决方案资源管理器中，右键单击解决方案并选择“设置启动项目...”。 
2. 选择“多启动项目”单选按钮。
3. 将两个项目的“操作”更改为“启动”。
4. 单击“确定”保存配置。
5. 按 **F5** 运行这两个应用程序。 各应用程序分别在自己的浏览器选项卡中打开。`https://localhost:44316/` 为 Web 应用。
    `https://localhost:44332/` 为 Web API。

6. 在 Web 应用中单击菜单横幅中的注册/登录链接，注册 Web 应用程序。 使用在 [Web 应用教程](active-directory-b2c-tutorials-web-app.md)中创建的帐户。 
7. 登录后，单击“待办事项列表”链接，创建待办事项列表项。

创建待办事项列表项时，Web 应用会向 Web API 发起生成待办事项列表项的请求。 Web 应用调用的是 Azure AD B2C 租户中的受保护 web API。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本文详细介绍了如何通过在 Azure AD B2C 中注册和定义作用域来保护 ASP.NET Web API。 若要更详细地了解如何制定此方案（包括代码演练），请继续下一教程。

> [!div class="nextstepaction"]
> [使用 Azure Active Directory B2C 注册、登录、个人资料编辑和密码重置创建 ASP.NET Web 应用](active-directory-b2c-devquickstarts-web-dotnet-susi.md)