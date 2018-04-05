---
title: Azure Active Directory B2C：QQ 配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 QQ 帐户的注册和登录功能。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.openlocfilehash: 12daed5f90e2b073a7520c061f18761baaed5c03
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C：向用户提供使用 QQ 帐户的注册和登录功能

> [!NOTE]
> 此功能为预览版。
> 

## <a name="create-a-qq-application"></a>创建 QQ 应用程序

要将 QQ 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 QQ 应用程序并向其提供合适的参数。 需要使用 QQ 帐户来完成此操作。 如果没有账户，可在 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) 处获取一个。

### <a name="register-for-the-qq-developer-program"></a>注册 QQ 开发人员计划

1. 转到 [QQ 开发人员门户](http://open.qq.com)并使用 QQ 帐户凭据登录。
2. 登录后，请转到 [http://open.qq.com/reg](http://open.qq.com/reg) 将自己注册为开发者。
3. 在菜单中，选择“个人”。
4. 在表单中输入所需的信息，并单击“下一步”。
5. 完成电子邮件验证过程。

> [!NOTE]
> 注册为开发人员后，需要等待几天，以获得批准。 

### <a name="register-a-qq-application"></a>注册 QQ 应用程序

1. 转到[https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
2. 单击“应用管理”。
3. 单击“创建应用”。
4. 输入所需的应用信息。
5. 单击“创建应用”。
6. 输入所需的信息。
7. 在“授权回调域”字段中，输入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果 `tenant_name` 是 contoso.onmicrosoft.com，请将 URL 设置为 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
8. 单击“创建应用”。
9. 在确认页上单击“应用管理”返回应用管理页。
10. 单击刚刚创建的应用旁边的“查看”。
11. 单击“修改”。
12. 在页面的顶部，复制“应用 ID”和“应用密钥”。

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>将 QQ 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“QQ”。
5. 单击“标识提供者类型”，选择“QQ”，并单击“确定”。
6. 单击“设置此标识提供者”
7. 输入先前作为**客户端 ID** 复制的**应用密钥**。
8. 输入先前作为**客户端机密**复制的**应用机密**。
9. 单击“确定”，并单击“创建”保存 QQ 配置。

