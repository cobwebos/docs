---
title: 使用 Azure Active Directory B2C 设置通过微信帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过微信帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e88187c5035abc28ca9deecaf8517e8a21e38d1d
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952328"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过微信帐户注册与登录

> [!NOTE]
> 此功能为预览版。
> 

## <a name="create-a-wechat-application"></a>创建 WeChat 应用程序

要将微信帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有微信帐户，可以在 [http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) 处获取信息。

### <a name="register-a-wechat-application"></a>注册微信应用程序

1. 使用微信凭据登录 [https://open.weixin.qq.com/](https://open.weixin.qq.com/)。
2. 选择“管理中心”(management center)。
3. 按照步骤注册新应用程序。
4. 在“授权回调域”(callback URL) 中输入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果 `tenant_name` 是 contoso.onmicrosoft.com，请将 URL 设置为 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
5. 复制“应用 ID”和“应用密钥”。 将标识提供者添加到租户时需要这两项。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>将微信配置为租户中的标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-wechat-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-wechat-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入“微信”。
6. 选择“标识提供者类型”，选择“微信(预览版)”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的应用 ID，并输入你记录为之前创建的微信应用程序的“客户端机密”的应用密钥。
8. 单击“确定”，并单击“创建”以保存微信配置。

