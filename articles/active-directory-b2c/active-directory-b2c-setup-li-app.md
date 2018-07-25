---
title: 使用 Azure Active Directory B2C 设置通过 LinkedIn 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 LinkedIn 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 84b1ad2ecd2c027c7d8a105579059ceb957f41c6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928992"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 LinkedIn 帐户注册与登录

## <a name="create-a-linkedin-application"></a>创建 LinkedIn 应用程序

要将 LinkedIn 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 LinkedIn 帐户，可以在 [https://www.linkedin.com/](https://www.linkedin.com/) 获取。

1. 使用 LinkedIn 帐户凭据登录到 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)。
2. 选择“我的应用”，然后单击“创建应用程序”。
3. 输入“公司名称”、“应用程序名称”、“应用程序说明”、“应用程序徽标”、“应用程序使用”、“网站 URL”、“企业电子邮件”和“商务电话”。
4. 同意“LinkedIn API 使用条款”并单击“提交”。
5. 复制“客户端 ID”和“客户端密钥”的值。 可在“身份验证密钥”下找到它们。 将 LinkedIn 配置为租户中的标识提供者时需要这两个值。 “客户端密钥”是一个重要的安全凭据。
6. 在“授权的重定向 URL”中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为租户名称（例如 contoso.onmicrosoft.com）。 选择“添加”，然后单击“更新”。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>将 LinkedIn 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-li-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-li-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入 LinkedIn。
6. 选择“标识提供者类型”，选择“LinkedIn”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID” 的客户端 ID，并输入你记录为之前创建的 LinkedIn 帐户应用程序的“客户端密码”的客户端密码。
8. 单击“确定”，然后单击“创建”以保存 LinkedIn 帐户配置。

