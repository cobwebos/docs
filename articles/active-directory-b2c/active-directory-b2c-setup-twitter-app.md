---
title: 使用 Azure Active Directory B2C 设置通过 Twitter 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为 应用程序中的客户提供通过 Twitter 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bf5ae39d83fd021775fbd18cf23d2e6b9078e748
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927890"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Twitter 帐户注册与登录

## <a name="create-a-twitter-application"></a>创建 Twitter 应用程序

要将 Twitter 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 Twitter 帐户，可以在 [https://twitter.com/signup](https://twitter.com/signup) 获取。

1. 使用 Twitter 凭据登录到 [Twitter 应用](https://apps.twitter.com/)。
2. 选择“创建新应用”。
3. 输入“名称”、“说明”和“网站”。
4. 在“回调 URL” 中输入 `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`。 将 {tenant} 替换为租户名称（例如 contosob2c.onmicrosoft.com），将 {policyId} 替换为策略 ID（例如 b2c_1_policy）。 应该为使用 Twitter 帐户的所有策略添加回调 URL。 如果在应用程序中使用它，请确保使用 `b2clogin.com` 而不是 ` login.microsoftonline.com`。
5. 同意“开发人员协议”，然后选择“创建 Twitter 应用程序”。
7. 选择“密钥和访问令牌”选项卡。
8. 复制“使用者密钥”和“使用者密码”的值。 将 Twitter 帐户配置为租户中的标识提供者时需要这两个值。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>将 Twitter 配置为租户中的标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入 Twitter。
6. 依次选择“标识提供者类型”、“Twitter”，然后单击“确定”。
7. 选择“设置此标识提供者”，针对“客户端 ID”，输入使用者密钥，针对“客户端密码”输入“使用者密码”。
8. 单击“确定”，并单击“创建”以保存 Twitter 配置。