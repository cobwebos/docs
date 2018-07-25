---
title: 使用 Azure Active Directory B2C 设置通过 QQ 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 QQ 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 82668446f139a5a003c33178e2d415a9314c61bc
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952171"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 QQ 帐户注册与登录

> [!NOTE]
> 此功能为预览版。
> 

## <a name="create-a-qq-application"></a>创建 QQ 应用程序

要将 QQ 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 QQ 帐户，可以在 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) 获取。

### <a name="register-for-the-qq-developer-program"></a>注册 QQ 开发人员计划

1. 使用 QQ 帐户凭据登录 [QQ 开发人员门户](http://open.qq.com)。
2. 登录后，请转到 [http://open.qq.com/reg](http://open.qq.com/reg) 将自己注册为开发者。
3. 选择“个人”(individual developer)。
4. 输入所需的信息，并选择“下一步” (next step)。
5. 完成电子邮件验证过程。 注册为开发人员后，需要等待几天，以获得批准。 

### <a name="register-a-qq-application"></a>注册 QQ 应用程序

1. 转到[https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
2. 单击“应用管理”(app management)。
5. 选择“创建应用” (create app)，然后输入所需的信息。
7. 在“授权回调域”(callback URL) 中输入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果 `tenant_name` 是 contoso.onmicrosoft.com，请将 URL 设置为 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
8. 选择“创建应用”(create app)。
9. 在确认页上选择“应用管理”(app management) 返回应用管理页。
10. 选择刚刚创建的应用旁边的“查看”(view)。
11. 选择“修改”(edit)。
12. 复制“应用 ID”和“应用密钥”。 将标识提供者添加到租户时需要这两个值。

## <a name="configure-qq-as-an-identity-provider"></a>将 QQ 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-qq-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-qq-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入“QQ”。
6. 选择“标识提供者类型”，选择“QQ (预览版)”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的应用 ID，并输入你记录为之前创建的 QQ 应用程序的“客户端机密”的应用密钥。
8. 单击“确定”，并单击“创建”保存 QQ 配置。

