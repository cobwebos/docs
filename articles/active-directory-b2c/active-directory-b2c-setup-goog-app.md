---
title: 使用 Google 帐户设置注册和登录-Azure Active Directory B2C
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Google 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7286bf1fd51883587aa41dc69d5dae0a3e6fb824
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622414"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Google 帐户注册与登录

## <a name="create-a-google-application"></a>创建 Google 应用程序

要将 Google 帐户用作 Azure Active Directory (Azure AD) B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 Google 帐户, 可以在上[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)注册。

1. 使用 Google 帐户凭据登录 [Google 开发人员控制台](https://console.developers.google.com/)。
1. 在页面的左上角, 选择 "项目" 列表, 然后选择 "**新建项目**"。
1. 输入“项目名称”，单击“创建”，然后确保使用的是新项目。
1. 在左侧菜单中选择“凭据”，然后选择“创建凭据” > “Oauth 客户端 ID”。
1. 在“应用程序类型”下，选择“Web 应用程序”。
1. 输入应用程序的名称，在已授权 JavaScript 来源中输入 `https://your-tenant-name.b2clogin.com`，并在已授权重定向 URI 中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
1. 单击“创建”。
1. 复制“客户端 ID”和“客户端密码”的值。 将 Google 配置为租户中的标识提供者时需要这两项。 **客户端密码**是一个重要的安全凭据。

## <a name="configure-a-google-account-as-an-identity-provider"></a>将 Google 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 通过在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含租户的目录, 确保使用的是包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**", 然后选择 " **Google**"。
1. 输入“名称”。 例如, *Google*。
1. 对于 "**客户端 id**", 请输入之前创建的 Google 应用程序的客户端 id。
1. 对于**客户端密码**, 请输入你记录的客户端密码。
1. 选择**保存**。
