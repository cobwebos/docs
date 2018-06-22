---
title: Azure Active Directory B2C 中的 Microsoft 帐户配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 Microsoft 帐户的注册和登录功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: be8c691835d2e89d5d305e01f9717e55e3741baf
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712413"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C：向用户提供使用 Microsoft 帐户的注册和登录功能
## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序
要将 Microsoft 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Microsoft 帐户应用程序并向其提供合适的参数。 需要使用 Microsoft 帐户来完成此操作。 如果没有账户，可在 [https://www.live.com/](https://www.live.com/) 处获取。

1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，并使用 Microsoft 帐户凭据登录。
2. 单击“添加应用程序”。
   
    ![Microsoft 帐户 — 添加新的应用程序](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. 提供应用程序的“名称”，单击“创建应用程序”。
   
    ![Microsoft 帐户 — 应用程序名称](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. 复制“应用程序 Id”的值。将 Microsoft 帐户配置为租户中的标识提供者时需要此项。
   
    ![Microsoft 帐户 — 应用程序 Id](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. 单击“添加平台”，并选择“Web”。
   
    ![Microsoft 帐户 — 添加平台](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Microsoft 帐户 — Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. 在“重定向 URI”字段中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 {tenant} 替换为租户名称（例如 contosob2c.onmicrosoft.com）。
   
    ![Microsoft 帐户 — 重定向 URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. 单击“应用程序密码”部分下的“生成新密码”。 复制屏幕上显示的新密码。 将 Microsoft 帐户配置为租户中的标识提供者时需要此项。 该密码是一个非常重要的安全凭据。
   
    ![Microsoft 帐户 — 生成新密码](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft 帐户 — 新密码](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. 选中“高级选项”部分下的“Live SDK 支持”复选框。 单击“ **保存**”。
   
    ![Microsoft 帐户 — Live SDK 支持](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>将 Microsoft 帐户配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“+ 添加”。
4. 为标识提供者配置提供一个友好“名称”。 例如，输入“MSA”。
5. 单击“标识提供者类型”，选择“Microsoft 帐户”，并单击“确定”。
6. 单击“设置此标识提供者”，输入之前创建的 Microsoft 帐户应用程序的应用程序 Id 和密码。
7. 单击“确定”，并单击“创建”以保存 Microsoft 帐户配置。

