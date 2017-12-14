---
title: "试用 Azure AD B2C 单页应用程序 | Microsoft Docs"
description: "使用 Azure AD B2C 测试环境进行登录、注册、编辑个人资料和重置密码等用户体验的试用"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: ba8ee4657309ab2a541f4c7b3fd4879542eee63c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>试用配置了 Azure AD B2C 的单页应用程序

## <a name="about-this-sample"></a>关于此示例

Azure Active Directory B2C 提供云身份管理来保护应用程序、业务和客户。  本快速入门使用示例单页应用程序演示以下操作：

* 使用“注册或登录”策略创建社交标识提供者或使用电子邮箱地址的本地帐户，或者通过它们登录。 
* 调用 API 从 Azure AD B2C 保护的资源检索显示名称。

## <a name="prerequisites"></a>先决条件

* 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - **ASP.NET 和 Web 开发**

* 安装 [Node.js](https://nodejs.org/en/download/)

* Facebook、Google、Microsoft 或 Twitter 中的社交帐户。 如果没有社交帐户，则必须提供有效的电子邮件地址。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

从 GitHub [下载或克隆示例应用程序](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

## <a name="run-the-sample-application"></a>运行示例应用程序

从 Node.js 命令提示符运行此示例： 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

控制台窗口显示计算机上运行的 Web 应用程序的端口号。

```
Listening on port 6420...
```

在 Web 浏览器中打开 `http://localhost:6420`访问 Web 应用程序。


![浏览器中的示例应用](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>创建帐户

单击“登录”按钮启动 Azure AD B2C “注册或登录”工作流。 创建帐户时，可以使用现有社交标识提供者帐户或者电子邮件帐户。

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交标识提供者注册

要使用社交标识提供者注册，请单击要使用的标识提供者按钮。 如果希望使用电子邮件地址，请跳转到[使用电子邮件地址注册](#sign-up-using-an-email-address)部分。

![登录或注册提供程序](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

需使用社交帐户凭据进行身份验证（登录）并授权应用程序读取社交帐户的信息。 通过授予访问权限，应用程序可以从社交帐户检索个人资料信息，如姓名和城市。 

![使用社交帐户进行身份验证和授权](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

新帐户个人资料详细信息已预先填充社交帐户的信息。 

![新帐户注册个人资料详细信息](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

更新“显示名称”、“职务”、“城市”字段，并单击“继续”。  输入的值用于 Azure AD B2C 用户帐户个人资料。

你已成功创建使用标识提供者的新 Azure AD B2C 用户帐户。 

下一步：[调用资源](#call-a-resource)部分。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

如果选择不使用社交帐户来提供身份验证，可以使用有效电子邮件地址创建 Azure AD B2C 用户帐户。 Azure AD B2C 本地用户帐户将 Azure Active Directory 用作标识提供者。 要使用电子邮件地址，请单击“没有帐户?立即注册”链接。

![使用电子邮件登录或注册](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

输入有效的电子邮件地址，然后单击“发送验证码”。 需要一个有效的电子邮件地址，用于接收来自 Azure AD B2C 的验证码。 

输入电子邮件中收到的验证码，然后单击“验证码”。

添加个人资料信息，然后单击“创建”。

![通过使用电子邮件的新帐户注册](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

你已成功创建新的 Azure AD B2C 本地用户帐户。

## <a name="call-a-resource"></a>调用资源

登录后，可以单击“调用 Web API”按钮，使显示名称作为 JSON 项目从 Web API 调用返回。 

![Web API 响应](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>后续步骤

下一步是创建自己的 Azure AD B2C 租户并将示例配置为使用租户运行。 

> [!div class="nextstepaction"]
> [在 Azure 门户中创建 Azure Active Directory B2C 租户](active-directory-b2c-get-started.md)