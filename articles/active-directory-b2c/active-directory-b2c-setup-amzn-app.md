---
title: 使用 Azure Active Directory B2C 设置通过 Amazon 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Amazon 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5bc1e0faf01125f498eb339bba841b96f6de9d82
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181798"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Amazon 帐户注册与登录

## <a name="create-an-amazon-application"></a>创建 Amazon 应用程序

要将 Amazon 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 Amazon 帐户，可以在 [http://www.amazon.com/](http://www.amazon.com/) 获取。

1. 使用 Amazon 帐户凭据登录 [Amazon 开发人员中心](https://login.amazon.com/)。
2. 如果未曾登录过，请单击“注册”，按照开发人员注册步骤，并接受策略。
3. 选择“注册新应用程序”。
4. 输入“名称”、“说明”、和“隐私声明 URL”，然后单击“保存”。 隐私声明是你管理的页面，用于向用户提供隐私信息。
5. 在“Web 设置”部分中，复制“客户端 ID”的值。 选择“显示机密”来获取客户端机密，然后复制它。 将 Amazon 帐户配置为租户中的标识提供者时需要这两个值。 “客户端密钥”是一个重要的安全凭据。
6. 在“Web 设置”部分中，选择“编辑”，然后在“允许的 JavaScript 来源”中输入 `https://your-tenant-name.b2clogin.com`并在“允许的返回 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
7. 单击“ **保存**”。

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>将 Amazon 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 输入“名称”。 例如，输入“Amazon”。
6. 选择“标识提供者类型”，选择“Amazon”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的客户端 ID，并输入你记录为之前创建的 Amazon 应用程序的“客户端机密”的客户端机密。
8. 单击“确定”，并单击“创建”以保存 Amazon 配置。

