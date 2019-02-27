---
title: 使用 GitHub 帐户设置注册与登录 - Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 GitHub 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f1c1ac91c08fe27445f4b9631500543d1d0287bd
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427269"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 GitHub 帐户注册与登录

> [!NOTE]
> 此功能为预览版。
> 

要将 GitHub 帐户用作 Azure Active Directory (Azure AD) B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 GitHub 帐户，可以在 [https://www.github.com/](https://www.github.com/) 获取。

## <a name="create-a-github-oauth-application"></a>创建 GitHub OAuth 应用程序

1. 使用 GitHub 凭据登录 [GitHub 开发人员](https://github.com/settings/developers)网站。
2. 选择“OAuth 应用”，然后选择“新建 OAuth 应用”。
3. 输入**应用程序名称**和**主页 URL**。
4. 在“授权回调 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 输入租户名称时，全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
5. 单击“注册应用程序”。
6. 复制“客户端 ID”和“客户端密钥”的值。 将标识提供者添加到租户时需要这两个值。

## <a name="configure-a-github-account-as-an-identity-provider"></a>将 GitHub 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入“GitHub”。
6. 选择“标识提供者类型”，选择“GitHub (预览版)”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的客户端 ID，并输入你记录为之前创建的 GitHub 帐户应用程序的“客户端密码”的客户端密码。
8. 单击“确定”，并单击“创建”以保存 GitHub 帐户配置。
