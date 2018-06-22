---
title: Azure Active Directory B2C 中的微信配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中向用户提供使用微信帐户的注册和登录功能。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bbdeccbdd0d6786fdf32fc2f547344b379bd0d7c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712481"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C：向用户提供使用微信帐户的注册和登录功能

> [!NOTE]
> 此功能为预览版。
> 

## <a name="create-a-wechat-application"></a>创建 WeChat 应用程序

要将微信用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建微信应用程序并向其提供合适的参数。 需要使用微信帐户来完成此操作。 如果没有帐户，可以通过其移动应用之一或使用 QQ 号注册来获得一个。 之后，获得已向微信开发人员计划注册的帐户。 可以在[此处](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)查看详细信息。

### <a name="register-a-wechat-application"></a>注册微信应用程序

1. 转到 [https://open.weixin.qq.com/](https://open.weixin.qq.com/) 并登录。
2. 单击“管理中心”(management center)。
3. 按照所需的步骤注册新应用程序。
4. 对于“授权回调域”(callback URL)，输入 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`。 例如，如果 `tenant_name` 是 contoso.onmicrosoft.com，请将 URL 设置为 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`。
5. 找到并复制**应用 ID** 和**应用密钥**。 稍后将需要这些项。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>将微信配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“+ 添加”。
4. 为标识提供者配置提供一个友好“名称”。 例如，输入“微信”。
5. 单击“标识提供者类型”，选择“微信”，并单击“确定”。
6. 单击“设置此标识提供者”
7. 输入先前作为**客户端 ID** 复制的**应用密钥**。
8. 输入先前作为**客户端机密**复制的**应用机密**。
9. 单击“确定”，并单击“创建”以保存微信配置。

