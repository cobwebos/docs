---
title: 使用 Azure Active Directory B2C 设置通过微信帐户注册与登录
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过微信帐户注册与登录的功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 377fd5c2be9e49e077303aac2a48fa2a0b8288ef
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622138"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过微信帐户注册与登录

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>创建 WeChat 应用程序

要将微信帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 WeChat 帐户, 可以在[https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)获取信息。

### <a name="register-a-wechat-application"></a>注册微信应用程序

1. 使用微信凭据登录 [https://open.weixin.qq.com/](https://open.weixin.qq.com/)。
1. 选择“管理中心”(management center)。
1. 按照步骤注册新应用程序。
1. 在“授权回调域”(callback URL) 中输入 `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 例如，如果租户名称是 contoso，请将 URL 设置为 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 复制“应用 ID”和“应用密钥”。 将标识提供者添加到租户时需要这两项。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>将微信配置为租户中的标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 通过在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含租户的目录, 确保使用的是包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**", 然后选择 " **WeChat (预览版)** "。
1. 输入“名称”。 例如, *WeChat*。
1. 对于 "**客户端 ID**", 请输入之前创建的 WeChat 应用程序的应用 ID。
1. 对于**客户端密码**, 请输入你记录的应用密钥。
1. 选择**保存**。
