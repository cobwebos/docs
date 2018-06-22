---
title: Azure Active Directory B2C 中的 Amazon 配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 Amazon 帐户的注册和登录功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9b3464e246a6b672362583ee4446a6146cc3b3d8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709744"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C：向用户提供使用 Amazon 帐户的注册和登录功能
## <a name="create-an-amazon-application"></a>创建 Amazon 应用程序
要将 Amazon 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Amazon 应用程序并向其提供合适的参数。 需要一个 Amazon 帐户来完成此操作。 如果没有账户，可在 [http://www.amazon.com/](http://www.amazon.com/) 处获取。

1. 转到 [Amazon 开发人员中心](https://login.amazon.com/)，使用 Amazon 帐户凭据登录。
2. 如果未曾登录过，请单击“注册”，按照开发人员注册步骤，并接受策略。
3. 单击“注册新应用程序”。
   
    ![在 Amazon 网站注册新应用程序](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. 提供应用程序信息（“名称”、“说明”和“隐私通知 URL”），并单击“保存”。
   
    ![提供用于在 Amazon 注册新的应用程序的应用程序信息](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. 在“Web 设置”部分，复制“客户端 ID”和“客户端密码”的值。 （需要单击“显示密码”按钮才能看到此信息。）将 Amazon 配置为租户中的标识提供者时需要这两个值。 在此部分底部单击“编辑”。 “客户端密钥”是一个重要的安全凭据。
   
    ![在 Amazon 中为新应用程序提供客户端 ID 和客户端密码](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. 在“允许的 JavaScript 来源”字段中输入 `https://login.microsoftonline.com`，在“允许的返回 URL”字段中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为租户名称（例如 contoso.onmicrosoft.com）。 单击“ **保存**”。 **{tenant}** 值区分大小写。
   
    ![在 Amazon 为新应用程序提供 JavaScript 来源和返回 URL](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>将 Amazon 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. 提供标识提供者配置的友好“名称”。 例如，输入“Amzn”。
5. 单击“标识提供者类型”，选择“Amazon”，并单击“确定”。
6. 单击“设置此标识提供者”，并输入之前创建的 Amazon 应用程序的客户端 ID 和客户端密码。
7. 单击“确定”，并单击“创建”以保存 Amazon 配置。

