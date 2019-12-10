---
title: 设置使用 Amazon 帐户的注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Amazon 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e43c880fb2e38a235c38e19854da9e2b6e2a10c0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947706"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Amazon 帐户注册与登录

## <a name="create-an-amazon-application"></a>创建 Amazon 应用程序

若要使用 Amazon 帐户作为 Azure Active Directory B2C （Azure AD B2C）中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要在租户中创建表示它的应用程序。 如果还没有 Amazon 帐户，可以[https://www.amazon.com/](https://www.amazon.com/)进行注册。

1. 使用 Amazon 帐户凭据登录 [Amazon 开发人员中心](https://login.amazon.com/)。
1. 如果未曾登录过，请单击“注册”，按照开发人员注册步骤，并接受策略。
1. 选择“注册新应用程序”。
1. 输入“名称”、“说明”、和“隐私声明 URL”，然后单击“保存”。 隐私声明是你管理的页面，用于向用户提供隐私信息。
1. 在“Web 设置”部分中，复制“客户端 ID”的值。 选择“显示机密”来获取客户端机密，然后复制它。 将 Amazon 帐户配置为租户中的标识提供者时需要这两个值。 “客户端密钥”是一个重要的安全凭据。
1. 在“Web 设置”部分中，选择“编辑”，然后在“允许的 JavaScript 来源”中输入 `https://your-tenant-name.b2clogin.com`并在“允许的返回 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
1. 单击“保存”。

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>将 Amazon 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**"，然后选择**Amazon**。
1. 输入“名称”。 例如， *Amazon*。
1. 对于 "**客户端 id**"，请输入之前创建的 Amazon 应用程序的客户端 id。
1. 对于**客户端密码**，请输入你记录的客户端密码。
1. 选择“保存”。
