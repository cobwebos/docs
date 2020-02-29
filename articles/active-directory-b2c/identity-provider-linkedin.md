---
title: 使用 LinkedIn 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 LinkedIn 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188094"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 LinkedIn 帐户注册与登录

## <a name="create-a-linkedin-application"></a>创建 LinkedIn 应用程序

若要在 Azure Active Directory B2C （Azure AD B2C）中将 LinkedIn 帐户用作[标识提供者](authorization-code-flow.md)，需要在租户中创建表示它的应用程序。 如果还没有 LinkedIn 帐户，可以在[https://www.linkedin.com/](https://www.linkedin.com/)上进行注册。

1. 使用 LinkedIn 帐户凭据登录到 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)。
1. 选择“我的应用”，然后单击“创建应用程序”。
1. 输入“公司名称”、“应用程序名称”、“应用程序说明”、“应用程序徽标”、“应用程序使用”、“网站 URL”、“企业电子邮件”和“商务电话”。
1. 同意“LinkedIn API 使用条款”并单击“提交”。
1. 复制“客户端 ID”和“客户端密钥”的值。 可在“身份验证密钥”下找到它们。 将 LinkedIn 配置为租户中的标识提供者时需要这两个值。 “客户端密钥”是一个重要的安全凭据。
1. 在“授权的重定向 URL”`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`**中输入** 。 将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。 选择“添加”，然后单击“更新”。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>将 LinkedIn 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**"，然后选择 " **LinkedIn**"。
1. 输入“名称”。 例如， *LinkedIn*。
1. 对于 "**客户端 id**"，请输入之前创建的 LinkedIn 应用程序的客户端 id。
1. 对于**客户端密码**，请输入你记录的客户端密码。
1. 选择“保存”。

## <a name="migration-from-v10-to-v20"></a>从1.0 版迁移到 v2。0

LinkedIn 最近[将其 api 从1.0 版更新到](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)了 v2.0。 作为迁移的一部分，Azure AD B2C 只能在注册过程中获取 LinkedIn 用户的完整名称。 如果电子邮件地址是在注册过程中收集的属性之一，则用户必须手动输入电子邮件地址并对其进行验证。
