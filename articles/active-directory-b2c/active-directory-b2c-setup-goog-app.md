---
title: 使用 Azure Active Directory B2C 设置通过 Google 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Google 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dd0bf50d73b70e37195e8e5e45336b68e4e883e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915633"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Google 帐户注册与登录

## <a name="create-a-google-application"></a>创建 Google 应用程序

要将 Google 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 Google 帐户，可以在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 获取。

1. 使用 Google 帐户凭据登录 [Google 开发人员控制台](https://console.developers.google.com/)。
2. 选择“创建项目”，然后单击“创建”。 如果之前已经创建了项目，则选择项目列表，然后选择“新项目”。
3. 输入“项目名称”，并单击“创建”。
3. 在左侧菜单中选择“凭据”，然后选择“创建凭据” > “Oauth 客户端 ID”。
4. 选择“配置许可屏幕”。
5. 选择或指定一个有效的电子邮件地址，提供向用户显示的产品名称，并单击“保存”。
6. 在“应用程序类型”下，选择“Web 应用程序”。
7. 输入应用程序的名称，在已授权 JavaScript 来源中输入 `https://login.microsoftonline.com`，并在已授权重定向 URI 中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 {tenant} 替换为租户名称（例如 contosob2c.onmicrosoft.com）。
8. 单击“创建”。
9. 复制“客户端 ID”和“客户端密码”的值。 将 Google 配置为租户中的标识提供者时需要这两项。 **客户端密码**是一个重要的安全凭据。

## <a name="configure-a-google-account-as-an-identity-provider"></a>将 Google 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 输入“名称”。 例如，输入“Google”。
6. 选择“标识提供者类型”，选择“Google”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的客户端 ID，并输入你记录为之前创建的 Google 帐户应用程序的“客户端机密”的客户端机密。
8. 单击“确定”，然后单击“创建”以保存 Google 配置。

