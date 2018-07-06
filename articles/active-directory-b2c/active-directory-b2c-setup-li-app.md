---
title: Azure Active Directory B2C 中的 LinkedIn 配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中，向具有 LinkedIn 帐户的使用者提供注册和登录。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7588711bd1c2a02e2e9a100d2ba182f43e7df488
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446052"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C：向具有 LinkedIn 帐户的使用者提供注册和登录
## <a name="create-a-linkedin-application"></a>创建 LinkedIn 应用程序
要将 LinkedIn 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 LinkedIn 应用程序并向其提供合适的参数。 需要一个 LinkedIn 帐户来完成此操作。 如果没有账户，可在 [https://www.linkedin.com/](https://www.linkedin.com/) 处获取。

1. 转到 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)，使用 LinkedIn 帐户凭据登录。
2. 在顶部菜单栏中单击“我的应用”，并单击“创建应用程序”。
   
    ![LinkedIn - 新建应用](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. 在“新建应用程序”表单中填写相关信息（“公司名称”、“名称”、“描述”、“应用程序徽标 URL”、“应用程序使用”、“网站 URL”、“业务电子邮件”和“业务电话”）。
4. 同意“LinkedIn API 使用条款”并单击“提交”。
   
    ![LinkedIn - 注册应用](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. 复制“客户端 ID”和“客户端密钥”的值。 （可在“身份验证密钥”下找到它们。）将 LinkedIn 配置为租户中的标识提供者时需要这两个值。
   
   > [!NOTE]
   > “客户端密钥”是一个重要的安全凭据。
   > 
   > 
6. 在（**OAuth 2.0** 下的）“授权重定向 URL”字段中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为租户名称（例如 contoso.onmicrosoft.com）。 单击“添加”，并单击“更新”。 {tenant} 值应为小写。
   
    ![LinkedIn - 安装应用](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>将 LinkedIn 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“+ 添加”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“LI”。
5. 单击“标识提供者类型”，选择“LinkedIn”，并单击“确定”。
6. 单击“设置此标识提供者”，并输入之前创建的 LinkedIn 应用程序的客户端 ID 和客户端密码。
7. 单击“确定”，并单击“创建”以保存 LinkedIn 配置。

