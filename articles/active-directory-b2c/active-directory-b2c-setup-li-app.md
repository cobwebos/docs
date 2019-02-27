---
title: 使用 LinkedIn 帐户设置注册与登录 - Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 LinkedIn 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6c340d6b410a289f8beca156f85472cb729b786a
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427813"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 LinkedIn 帐户注册与登录

## <a name="create-a-linkedin-application"></a>创建 LinkedIn 应用程序

若要将 LinkedIn 帐户用作 Azure Active Directory (Azure AD) B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 LinkedIn 帐户，可以在 [https://www.linkedin.com/](https://www.linkedin.com/) 获取。

1. 使用 LinkedIn 帐户凭据登录到 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)。
2. 选择“我的应用”，然后单击“创建应用程序”。
3. 输入“公司名称”、“应用程序名称”、“应用程序说明”、“应用程序徽标”、“应用程序使用”、“网站 URL”、“企业电子邮件”和“商务电话”。
4. 同意“LinkedIn API 使用条款”并单击“提交”。
5. 复制“客户端 ID”和“客户端密钥”的值。 可在“身份验证密钥”下找到它们。 将 LinkedIn 配置为租户中的标识提供者时需要这两个值。 “客户端密钥”是一个重要的安全凭据。
6. 在“授权的重定向 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。 选择“添加”，然后单击“更新”。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>将 LinkedIn 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入 LinkedIn。
6. 选择“标识提供者类型”，选择“LinkedIn”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID” 的客户端 ID，并输入你记录为之前创建的 LinkedIn 帐户应用程序的“客户端密码”的客户端密码。
8. 单击“确定”，然后单击“创建”以保存 LinkedIn 帐户配置。

