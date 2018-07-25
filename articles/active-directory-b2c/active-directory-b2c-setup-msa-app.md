---
title: 使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Microsoft 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16e4dbac4c8146b048d4d9b76544677a6111e2a5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900823"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序

要将 Microsoft 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 Microsoft 帐户，可以在 [https://www.live.com/](https://www.live.com/) 获取。

1. 使用 Microsoft 帐户凭据登录 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。
2. 在右上角，选择“添加应用”。
3. 提供应用程序的“名称”，并单击“创建”。
4. 在注册页上，复制“应用程序 ID”的值。使用该值将 Microsoft 帐户配置为租户中的标识提供者。
5. 选择“添加平台”，然后选择“Web”。
6. 在“重定向 URL”中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 {tenant} 替换为租户名称（例如 contosob2c.onmicrosoft.com）。
7. 在“应用程序机密”下，选择“生成新密码”。 复制屏幕上显示的新密码。 将 Microsoft 帐户配置为租户中的标识提供者时需要此项。 该密码是一个非常重要的安全凭据。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>将 Microsoft 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入“MSA”。
6. 选择“标识提供者类型”，选择“Microsoft 帐户”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的应用程序 ID，并输入你记录为之前创建的 Microsoft 帐户应用程序的“客户端机密”的密码。
8. 单击“确定”，并单击“创建”以保存 Microsoft 帐户配置。

