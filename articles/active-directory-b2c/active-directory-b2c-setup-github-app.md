---
title: Azure Active Directory B2C 中的 GitHub 标识提供者配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 GitHub 帐户的注册和登录功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3754a169b301bac97f3e12d10b754222e3cf325d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443335"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C：向使用者提供使用 GitHub 帐户的注册和登录功能

> [!NOTE]
> 此功能为预览版。
> 

本文介绍如何为具有 GitHub 帐户的用户启用登录。

## <a name="create-a-github-oauth-application"></a>创建 GitHub OAuth 应用程序

要将 GitHub 用作 Azure AD B2C 中的标识提供者，需要创建 GitHub OAuth 应用并向其提供合适的参数。

1. 登录到 GitHub 之后，转到 [GitHub 开发人员设置](https://github.com/settings/developers)。
1. 单击“新建 OAuth 应用”
1. 输入**应用程序名称**和**主页 URL**。
1. 对于“授权回调 URL”，输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 {tenant} 替换为 Azure AD B2C 租户名称（例如 contosob2c.onmicrosoft.com）。

    >[!NOTE]
    >“登录 URL”中的“tenant”的值必须全小写。

1. 单击“注册应用程序”。
1. 保存“客户端 ID”和“客户端密码”的值。 在下一部分中将用到它们。

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>将 GitHub 配置为 Azure AD B2C 租户中的标识提供者

1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
1. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
1. 单击边栏选项卡顶部的“+ 添加”。
1. 为标识提供者配置提供一个友好“名称”。 例如，输入“GitHub”。
1. 单击“标识提供者类型”，选择“GitHub”，并单击“确定”。
1. 单击“设置此标识提供者”，并输入之前复制的 GitHub OAuth 应用程序的客户端 ID 和客户端密码。
1. 单击“确定”，并单击“创建”以保存 GitHub 配置。

## <a name="next-steps"></a>后续步骤

创建或编辑[内置策略](active-directory-b2c-reference-policies.md)并将 GitHub 添加为标识提供者。