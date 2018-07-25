---
title: 使用 Azure Active Directory B2C 设置通过 Amazon 帐户注册与登录 | Microsoft Docs
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Amazon 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5fb6289f75f0c98cc218233d8adb900484ee4a17
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916490"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Amazon 帐户注册与登录

## <a name="create-an-amazon-application"></a>创建 Amazon 应用程序

要将 Amazon 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 Amazon 帐户，可以在 [http://www.amazon.com/](http://www.amazon.com/) 获取。

1. 使用 Amazon 帐户凭据登录 [Amazon 开发人员中心](https://login.amazon.com/)。
2. 如果未曾登录过，请单击“注册”，按照开发人员注册步骤，并接受策略。
3. 选择“注册新应用程序”。
4. 输入“名称”、“说明”、和“隐私声明 URL”，然后单击“保存”。
5. 在“Web 设置”部分中，复制“客户端 ID”的值。 选择“显示机密”来获取客户端机密，然后复制它。 将 Amazon 帐户配置为租户中的标识提供者时需要这两个值。 “客户端密钥”是一个重要的安全凭据。
6. 在“Web 设置”部分中，选择“编辑”，然后在“允许的 JavaScript 来源”中输入 `https://login.microsoftonline.com`并在“允许的返回 URL”中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为租户名称（例如 contoso.onmicrosoft.com）。 
7. 单击“ **保存**”。

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>将 Amazon 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择订阅信息，然后选择“切换目录”。 

    ![切换到 Azure AD B2C 租户](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    选择包含租户的目录。

    ![选择目录](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 输入“名称”。 例如，输入“Amazon”。
6. 选择“标识提供者类型”，选择“Amazon”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的客户端 ID，并输入你记录为之前创建的 Amazon 应用程序的“客户端机密”的客户端机密。
8. 单击“确定”，并单击“创建”以保存 Amazon 配置。

