---
title: 通过 Google 帐户设置注册与登录 - Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Google 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7e092e27f73901810a9f7edd210e3513c54095e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508504"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Google 帐户注册与登录

## <a name="create-a-google-application"></a>创建 Google 应用程序

要将 Google 帐户用作 Azure Active Directory (Azure AD) B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 Google 帐户，可以在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 获取。

1. 使用 Google 帐户凭据登录 [Google 开发人员控制台](https://console.developers.google.com/)。
2. 在页面的左上角，选择项目列表中，，然后选择**新的项目**。
3. 输入  “项目名称”，单击  “创建”，然后确保使用的是新项目。
4. 在左侧菜单中选择“凭据”  ，然后选择“创建凭据”   > “Oauth 客户端 ID”  。
5. 在“应用程序类型”  下，选择“Web 应用程序”  。
6. 输入应用程序的名称  ，在已授权 JavaScript 来源  中输入 `https://your-tenant-name.b2clogin.com`，并在已授权重定向 URI  中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
7. 单击**创建**。
8. 复制“客户端 ID”  和“客户端密码”  的值。 将 Google 配置为租户中的标识提供者时需要这两项。 **客户端密码**是一个重要的安全凭据。

## <a name="configure-a-google-account-as-an-identity-provider"></a>将 Google 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”  ，然后选择“添加”  。
5. 输入“名称”  。 例如，输入“Google”  。
6. 选择“标识提供者类型”  ，选择“Google”  ，并单击“确定”  。
7. 选择“设置此标识提供者”  ，然后输入你之前记录为“客户端 ID”  的客户端 ID，并输入你记录为之前创建的 Google 帐户应用程序的“客户端机密”  的客户端机密。
8. 单击“确定”  ，然后单击“创建”  以保存 Google 配置。

