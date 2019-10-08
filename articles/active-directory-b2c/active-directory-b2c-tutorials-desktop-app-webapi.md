---
title: 教程 - 从桌面应用程序授予对 Node.js Web API 的访问权限 - Azure Active Directory B2C | Microsoft Docs
description: 有关如何从 .NET 桌面应用使用 Active Directory B2C 保护 .Node.js Web API 并调用该 API 的教程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679328"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>教程：从桌面应用使用 Azure Active Directory B2C 授予对 Node.js Web API 的访问权限

本教程展示了如何从 Windows Presentation Foundation (WPF) 桌面应用调用受 Active Directory B2C (Azure AD B2C) 保护的 Node.js Web API 资源。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例更新为使用该应用程序

## <a name="prerequisites"></a>先决条件

完成[教程：使桌面应用能够使用帐户通过 Azure Active Directory B2C 进行身份验证](active-directory-b2c-tutorials-desktop-app.md)。

## <a name="add-a-web-api-application"></a>添加 Web API 应用程序

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>配置范围

可通过范围控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让某些用户拥有读取和写入访问权限，让另一些用户拥有只读权限。 在本教程中，你将定义对 Web API 的读取权限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>授予权限

若要从应用程序调用受保护的 Web API，需授予应用程序访问该 API 的权限。 在先决条件教程中，已在 Azure AD B2C 中创建名为 *app1* 的 Web 应用程序。 使用此应用程序调用 Web API。

1. 依次选择“应用程序”、“nativeapp1”   。
1. 依次选择“API 访问”、“添加”   。
1. 在“选择 API”下拉列表中，选择“webapi1”   。
1. 在“选择范围”  下拉列表中，选择先前定义的范围。 例如，*demo.read* 和 *demo.write*。
1. 选择“确定”  。

用户通过 Azure AD B2C 进行身份验证，以使用 WPF 桌面应用程序。 桌面应用程序从 Azure AD B2C 获取授权，以访问受保护的 Web API。

## <a name="configure-the-sample"></a>配置示例

注册 Web API 并定义范围之后，请将 Web API 代码配置为使用你的 Azure AD B2C 租户。 本教程将配置一个可从 GitHub 下载的示例 Node.js Web 应用程序。

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)或克隆示例 Web 应用。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

该 Node.js Web API 示例使用 Passport.js 库来使得 Azure AD B2C 能够保护对 API 的调用。

1. 打开 `index.js` 文件。
2. 使用 Azure AD B2C 租户注册信息来配置示例。 更改以下代码行：

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>运行示例

1. 启动 Node.js 命令提示符。
2. 切换到包含 Node.js 示例的目录。 例如 `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 运行以下命令：
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>运行桌面应用程序

1. 在 Visual Studio 中打开 **active-directory-b2c-wpf** 解决方案。
2. 按 **F5** 来运行桌面应用。
3. 使用[在桌面应用中使用 Azure Active Directory B2C 对用户进行身份验证](active-directory-b2c-tutorials-desktop-app.md)教程中使用的电子邮件地址和密码进行登录。
4. 单击“调用 API”  按钮。

桌面应用程序将向 Web API 发出请求并得到其中包含已登录用户的显示名称的响应。 受保护的桌面应用程序正在调用 Azure AD B2C 租户中的受保护 Web API。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加 Web API 应用程序
> * 配置 Web API 的范围
> * 授予 Web API 权限
> * 将示例更新为使用该应用程序

> [!div class="nextstepaction"]
> [教程：将标识提供者添加到 Azure Active Directory B2C 中的应用程序](tutorial-add-identity-providers.md)
