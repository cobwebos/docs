---
title: "试用 Azure AD B2C 桌面应用程序 | Microsoft Docs"
description: "使用 Azure AD B2C 测试环境进行登录、注册、编辑个人资料和重置密码等用户体验的试用"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 9653d86dd5635016512bf6e6fafbf7195145ed07
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>试用配置了 Azure AD B2C 的桌面应用程序

Azure Active Directory B2C 提供云身份管理来保护应用程序、业务和客户。  本快速入门使用示例 Windows Presentation Foundation (WPF) 桌面应用演示以下操作：

* 使用“注册或登录”策略创建社交标识提供者或使用电子邮箱地址的本地帐户，或者通过它们登录。 
* 调用 API 从 Azure AD B2C 保护的资源检索显示名称。

## <a name="prerequisites"></a>先决条件

* 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - .NET 桌面开发

* Facebook、Google、Microsoft 或 Twitter 中的社交帐户。 如果没有社交帐户，则必须提供有效的电子邮件地址。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

从 GitHub [下载或克隆示例应用程序](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)。

## <a name="run-the-app-in-visual-studio"></a>在 Visual Studio 中运行应用

在示例应用程序项目文件夹中，打开 Visual Studio 中的 `active-directory-b2c-wpf.sln` 解决方案。 

选择“调试”>“开始调试”生成并运行应用程序。 

## <a name="create-an-account"></a>创建帐户

单击“登录”开始“注册或登录”工作流。 创建帐户时，可以使用现有社交标识提供者帐户或者电子邮件帐户。

![示例应用程序](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>使用社交标识提供者注册

要使用社交标识提供者注册，请单击要使用的标识提供者按钮。 如果希望使用电子邮件地址，请跳转到[使用电子邮件地址注册](#sign-up-using-an-email-address)部分。

![登录或注册提供程序](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

需使用社交帐户凭据进行身份验证（登录）并授权应用程序读取社交帐户的信息。 通过授予访问权限，应用程序可以从社交帐户检索个人资料信息，如姓名和城市。 

![使用社交帐户进行身份验证和授权](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

新帐户个人资料详细信息已预先填充社交帐户的信息。 按需修改详细信息并单击“继续”。

![新帐户注册个人资料详细信息](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

你已成功创建使用标识提供者的新 Azure AD B2C 用户帐户。 登录后，访问令牌显示在“令牌信息”文本框中。 访问令牌在访问 API 资源时使用。

![授权令牌](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

下一步：[跳转以编辑个人资料](#edit-your-profile)部分。

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

如果选择不使用社交帐户来提供身份验证，可以使用有效电子邮件地址创建 Azure AD B2C 用户帐户。 Azure AD B2C 本地用户帐户将 Azure Active Directory 用作标识提供者。 要使用电子邮件地址，请单击“没有帐户?立即注册”链接。

![使用电子邮件登录或注册](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

输入有效的电子邮件地址，然后单击“发送验证码”。 需要一个有效的电子邮件地址，用于接收来自 Azure AD B2C 的验证码。

输入电子邮件中收到的验证码，然后单击“验证码”。

添加个人资料信息，然后单击“创建”。

![通过使用电子邮件的新帐户注册](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

你已成功创建新的 Azure AD B2C 本地用户帐户。 登录后，访问令牌显示在“令牌信息”文本框中。 访问令牌在访问 API 资源时使用。

![授权令牌](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>编辑个人资料

Azure Active Directory B2C 提供允许用户更新个人资料的功能。 单击“编辑个人资料”编辑创建的个人资料。

![编辑个人资料](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

选择与创建的帐户相关联的标识提供者。 例如，如果创建帐户时使用 Twitter 作为标识提供者，那么选择使用 Twitter 来修改关联的个人资料详细信息。

![选择与要编辑的个人资料关联的提供程序](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

更改“显示名称”或“城市”。 

![更新配置文件](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

新的访问令牌显示在“令牌信息”文本框中。 如果要验证对个人资料的更改，将访问令牌复制并粘贴到令牌解码器 (https://jwt.ms) 中。

![授权令牌](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>访问资源

单击“调用 API”对 Azure AD B2C 保护的资源 https://fabrikamb2chello.azurewebsites.net/hello 提出请求。 

![调用 API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

应用程序包括请求的“令牌信息”文本框中显示的访问令牌。 API 发送回包含在访问令牌中的显示名称。

## <a name="next-steps"></a>后续步骤

下一步是创建自己的 Azure AD B2C 租户并将示例配置为使用租户运行。 

> [!div class="nextstepaction"]
> [在 Azure 门户中创建 Azure Active Directory B2C 租户](active-directory-b2c-get-started.md)
