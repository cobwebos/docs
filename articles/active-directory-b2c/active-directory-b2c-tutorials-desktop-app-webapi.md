---
title: 教程 - 从桌面应用程序授予对 Node.js Web API 的访问权限 - Azure Active Directory B2C | Microsoft Docs
description: 有关如何从 .NET 桌面应用使用 Active Directory B2C 保护 .Node.js Web API 并调用该 API 的教程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 35d850dc6439173c83730375c576855f6920b450
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475246"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>教程：从桌面应用使用 Azure Active Directory B2C 授予对 Node.js Web API 的访问权限

本教程展示了如何从 Windows Presentation Foundation (WPF) 桌面应用（也受 Azure AD B2C 保护）调用受 Azure Active Directory B2C (Azure AD B2C) 保护的 Node.js Web API。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例更新为使用该应用程序

## <a name="prerequisites"></a>先决条件

完成[教程：在本机桌面客户端中对用户进行身份验证](active-directory-b2c-tutorials-desktop-app.md)。

## <a name="add-a-web-api-application"></a>添加 Web API 应用程序

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>配置范围

可通过范围控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让某些用户拥有读取和写入访问权限，让另一些用户拥有只读权限。 在本教程中，请为 Web API 定义读取和写入权限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

记下 `demo.read` 范围对应的“范围”  下的值，以便在稍后的步骤中配置桌面应用程序时使用。 完整范围值类似于 `https://contosob2c.onmicrosoft.com/api/demo.read`。

## <a name="grant-permissions"></a>授予权限

若要从本机客户端应用程序调用受保护的 Web API，需要向在 Azure AD B2C 中注册的 Web API 授予已注册的本机客户端应用程序权限。

在先决条件教程中，你注册了名为“nativeapp1”  的本机客户端应用程序。 以下步骤使用你向上述部分中“webapi1”  公开的 API 范围来配置本机应用程序注册。 这允许桌面应用程序从 Azure AD B2C 获取访问令牌，Web API 可以使用该令牌来验证和提供对其资源的作用域访问。 在本教程的后面部分，你将配置并运行桌面应用程序和 Web API 代码示例。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 依次选择“应用程序”、“nativeapp1”   。
1. 依次选择“API 访问”、“添加”   。
1. 在“选择 API”下拉列表中，选择“webapi1”   。
1. 在“选择范围”  下拉列表中，选择先前定义的范围。 例如，*demo.read* 和 *demo.write*。
1. 选择“确定”  。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册(预览版)](#tab/app-reg-preview/)

1. 选择“应用注册(预览版)”  ，然后选择应该有权访问 API 的 本机客户端应用程序。 例如，“nativeapp1”  。
1. 在“管理”下选择“API 权限”  。 
1. 在“已配置权限”  下，选择“添加权限”  。
1. 选择“我的 API”  选项卡。
1. 选择应授予本机客户端应用程序访问权限的 API。 例如，“webapi1”  。
1. 在“权限”  下，展开“演示”  ，然后选择先前定义的范围。 例如，*demo.read* 和 *demo.write*。
1. 选择“添加权限”  。 按照指示，请等待几分钟，然后再继续下一步。
1. 选择“向(你的租户姓名)授予管理员同意”  。
1. 选择当前登录的管理员帐户，或者使用至少分配了“云应用程序管理员”  角色的 Azure AD B2C 租户中的帐户登录。
1. 选择“接受”  。
1. 选择“刷新”  ，然后验证两个范围的“状态”  下是否均显示“授权给...”。 传播权限可能需要几分钟时间。

* * *

用户通过 Azure AD B2C 进行身份验证，以使用 WPF 桌面应用程序。 桌面应用程序从 Azure AD B2C 获取授权，以访问受保护的 Web API。

## <a name="configure-the-samples"></a>配置示例

现在已注册 Web API 并已配置范围和权限，请将桌面应用程序和 Web API 示例配置为使用 Azure AD B2C 租户。

### <a name="update-the-desktop-application"></a>更新桌面应用程序

在本文的先决条件中，你修改了 [WPF 桌面应用程序](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)以便能够在 Azure AD B2C 租户中使用用户流进行登录。 在本部分中，将更新此同一应用程序以引用之前注册的 Web API，即“webapi1”  。

1. 在 Visual Studio 中打开 active-directory-b2c-wpf  解决方案 (`active-directory-b2c-wpf.sln`)。
1. 在 active-directory-b2c-wpf  项目中，打开 App.xaml.cs  文件并查找以下变量定义。
    1. 将 `ApiScopes` 变量的值替换为之前你在定义 demo.read  范围时记录的值。
    1. 将 `ApiEndpoint` 变量的值替换为之前在你的租户中注册 Web API（例如 webapi1  ）时记录的“重定向 URI”  。

    下面是一个示例：

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>获取和更新 Node.js API 示例

接下来，从 GitHub 获取 Node.js Web API 代码示例，并将其配置为使用你在 Azure AD B2C 租户中注册的 Web API。

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)或克隆示例 Web 应用。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

该 Node.js Web API 示例使用 Passport.js 库来使得 Azure AD B2C 能够保护对 API 的调用。

1. 打开 `index.js` 文件。
1. 使用以下值更新这些变量定义。 将 `<web-API-application-ID>` 更改为之前注册的 Web API (webapi1  ) 的“应用程序(客户端) ID”  。 将 `<your-b2c-tenant>` 更改为 Azure AD B2C 租户的名称。

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. 由于你是在本地运行 API，因此请将 GET 方法的路由中的路径更新为 `/` 而不是 `/hello` 的演示应用位置：

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>运行示例

### <a name="run-the-nodejs-web-api"></a>运行 Node.js Web API

1. 启动 Node.js 命令提示符。
2. 切换到包含 Node.js 示例的目录。 例如 `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 运行以下命令：
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>运行桌面应用程序

1. 在 Visual Studio 中打开 **active-directory-b2c-wpf** 解决方案。
2. 按 **F5** 来运行桌面应用。
3. 使用[在桌面应用中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-desktop-app.md)教程中使用的电子邮件地址和密码进行登录。
4. 选择“调用 API”  按钮。

桌面应用程序向本地运行的 Web API 发出请求，并在验证有效的访问令牌时显示已登录用户的显示名称。

![显示名称将显示在 WPF 桌面应用程序的顶部窗格中](media/active-directory-b2c-tutorials-desktop-app-webapi/desktop-app-01-post-api-call.png)

你的桌面应用程序（受 Azure AD B2C 保护）将调用本地运行的 Web API，其中此 API 也受 Azure AD B2C 保护。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例更新为使用该应用程序

> [!div class="nextstepaction"]
> [教程：将标识提供者添加到 Azure Active Directory B2C 中的应用程序](tutorial-add-identity-providers.md)
