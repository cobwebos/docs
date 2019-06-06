---
title: 使用 Azure Active Directory B2C 设置通过 Twitter 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为 应用程序中的客户提供通过 Twitter 帐户注册与登录的功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 706807039ac7c13cd88bc8ac99acf0d7ece3b384
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508149"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Twitter 帐户注册与登录

## <a name="create-an-application"></a>创建应用程序

若要将 Twitter 用作 Azure AD B2C 中的身份提供程序，则需要创建 Twitter 应用程序。 如果还没有 Twitter 帐户，可以在 [https://twitter.com/signup](https://twitter.com/signup) 获取。

1. 使用 Twitter 帐户凭据登录到 [Twitter 开发人员](https://developer.twitter.com/en/apps)网站。
2. 选择“创建应用”。 
3. 输入“应用名称”和“应用程序说明”   。
4. 在“网站 URL”  中，输入 `https://your-tenant.b2clogin.com`。 将 `your-tenant` 替换为租户的名称。 例如， https://contosob2c.b2clogin.com。
5. 对于“回调 URL”  ，输入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`。 将 `your-tenant` 替换为你的租户的名称，并将 `your-user-flow-Id` 替换为你的用户流的标识符。 例如，`b2c_1A_signup_signin_twitter`。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
6. 在页面底部，阅读并接受条款，然后选择“创建”。 
7. 在“应用详细信息”  页上，选择“编辑”>“编辑详细信息”  ，勾选“启用 Twitter 登录”  框，然后选择“保存”  。
8. 选择“密钥和令牌”  并记录“使用者 API 密钥”  和“使用者 API 密钥”  的值，以便稍后使用。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>将 Twitter 配置为租户中的标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”  ，然后选择“添加”  。
5. 提供“名称”  。 例如，输入 Twitter  。
6. 依次选择“标识提供者类型”  、“Twitter”  ，然后单击“确定”  。
7. 选择“设置此身份提供程序”  ，针对“客户端 ID”  ，输入 API 密钥，针对“客户端密码”输入 API 密钥  。
8. 单击“确定”  ，并单击“创建”  以保存 Twitter 配置。
