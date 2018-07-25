---
title: 使用 Azure Active Directory B2C 设置通过 Facebook 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Facebook 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: de059e3875b5f15526cb176d43a019fd2d9ee9b9
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901375"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Facebook 帐户注册与登录

## <a name="create-a-facebook-application"></a>创建 Facebook 应用程序

要将 Facebook 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 Facebook 帐户，可以在 [https://www.facebook.com/](https://www.facebook.com/) 获取。

1. 使用 Facebook 帐户凭据登录 [Facebook 开发人员](https://developers.facebook.com/)。
2. 如果以前没有登录过，需要注册为 Facebook 开发人员。 要执行此操作，请在页面右上角选择“注册”，接受 Facebook 的策略，并完成注册步骤。
3. 选择“我的应用”，然后单击“添加新应用”。 
4. 输入“显示名称”和有效的“联系人电子邮件”。
5. 单击“创建应用程序 ID”。 这会要求接受 Facebook 平台策略并完成在线安全检查。
6. 选择“设置” > “基本”。
7. 在页面底部，选择“添加平台”，然后选择“网站”。
8. 在“网站 URL”中输入 `https://login.microsoftonline.com/`。 为“隐私策略 URL”输入一个 URL，例如 `http://www.contoso.com`。
9. 选择“保存更改”。
11. 在页面的顶部，复制“应用 ID”的值。 
12. 单击“显示”，并复制“应用程序密码”的值。 使用这两个值将 Facebook 配置为租户中的标识提供者。 “应用程序密码”是一个非常重要的安全凭据。
13. 选择“产品”，然后在“Facebook 登录”下选择“设置”。
14. 在“Facebook 登录”下选择“设置”。
15. 在“有效的 OAuth 重定向 URL”中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 {tenant} 替换为租户名称（例如 contosob2c.onmicrosoft.com）。 单击页面底部的“保存更改”。
16. 若要使 Facebook 应用程序可在 Azure AD B2C 中使用，选择“应用审核”，并将“公开我的应用程序?”设置为“是”，选择一个类别，例如 `Business and Pages`，然后单击“确认”。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>将 Facebook 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 输入“名称”。 例如，输入“Facebook”。
6. 选择“标识提供者类型”，选择“Facebook”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的应用 ID，并输入你记录为之前创建的 Facebook 应用程序的“客户端机密”的应用机密。
8. 单击“确定”，并单击“创建”以保存 Facebook 配置。