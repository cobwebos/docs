---
title: 快速入门 - 使用 Azure Active Directory B2C 为单页应用设置登录 | Microsoft Docs
description: 运行一个使用 Azure Active Directory B2C 提供帐户登录的示例单页应用程序。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 2/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7e600f55b9eeb2682b005b8c84c74ec25e054c36
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447245"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>快速入门：使用 Azure Active Directory B2C 为单页应用设置登录

Azure Active Directory (Azure AD) B2C 提供云身份管理来保护应用程序、业务和客户。 应用可以使用 Azure AD B2C 通过开放式标准协议对社交帐户和企业帐户进行身份验证。

在本教程中，请使用启用了 Azure AD B2C 的示例单页应用通过社交标识提供者来登录，并调用受 Azure AD B2C 保护的 Web API。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 带有 ASP.NET 和 Web 开发工作负荷的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* 安装 [Node.js](https://nodejs.org/en/download/)
* Facebook、Google、Microsoft 或 Twitter 中的社交帐户。

## <a name="download-the-sample"></a>下载示例

从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)或克隆示例 Web 应用。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="run-the-sample-application"></a>运行示例应用程序

从 Node.js 命令提示符运行此示例： 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

Node.js 应用输出在 localhost 中侦听的端口号。

```
Listening on port 6420...
```

在 Web 浏览器中浏览到应用的 URL `http://localhost:6420`。

![浏览器中的示例应用](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>创建帐户

单击“登录”按钮，启动基于 Azure AD B2C 策略的 Azure AD B2C “注册或登录”工作流。 

此示例支持多个注册选项，包括使用社交标识提供者，或者使用电子邮件地址来创建本地帐户。 对于本快速入门，将使用 Facebook、Google、Microsoft 或 Twitter 社交标识提供者帐户。 

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交标识提供者注册

Azure AD B2C 为示例 Web 应用的虚构品牌“Wingtip Toys”提供了一个自定义登录页。 

1. 要使用社交标识提供者注册，请单击要使用的标识提供者按钮。

    ![登录或注册提供程序](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    请使用社交帐户凭据进行身份验证（登录）并授权应用程序读取社交帐户的信息。 通过授予访问权限，应用程序可以从社交帐户检索个人资料信息，如姓名和城市。 

2. 完成标识提供者的登录进程。 例如，如果你选择 Twitter，请输入你的 Twitter 凭据，然后单击“登录”。

    ![使用社交帐户进行身份验证和授权](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

    新帐户个人资料详细信息已预先填充社交帐户的信息。 

3. 更新“显示名称”、“职务”、“城市”字段，并单击“继续”。  输入的值用于 Azure AD B2C 用户帐户个人资料。

    你已成功创建使用标识提供者的新 Azure AD B2C 用户帐户。 

## <a name="access-a-protected-web-api-resource"></a>访问受保护的 Web API 资源

单击“调用 Web API”按钮，使显示名称作为 JSON 对象从 Web API 调用返回。 

![Web API 响应](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

示例性的单页应用在对受保护的 Web API 资源的请求中包括了 Azure AD 访问令牌，该请求的目的是执行返回 JSON 对象的操作。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 快速入门或教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用启用了 Azure AD B2C 的示例 ASP.NET 应用执行以下操作：使用自定义登录页登录、使用社交标识提供者登录、创建 Azure AD B2C 帐户，以及调用受 Azure AD B2C 保护的 Web API。 

下一步是创建自己的 Azure AD B2C 租户并将示例配置为使用租户运行。 

> [!div class="nextstepaction"]
> [在 Azure 门户中创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)