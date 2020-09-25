---
title: 使用 LinkedIn 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 LinkedIn 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259452"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 LinkedIn 帐户注册与登录

## <a name="create-a-linkedin-application"></a>创建 LinkedIn 应用程序

要将 LinkedIn 帐户用作 Azure Active Directory B2C (Azure AD B2C) 中的[标识提供者](authorization-code-flow.md)，需要在表示它的租户中创建一个应用程序。 如果你没有 LinkedIn 帐户，可在 [https://www.linkedin.com/](https://www.linkedin.com/) 上注册一个。

1. 使用 LinkedIn 帐户凭据登录到 [LinkedIn 开发人员网站](https://www.developer.linkedin.com/)。
1. 选择 **"我的应用**"，然后单击 " **创建应用**"。
1. 输入 **应用名称**、 **LinkedIn 页**、 **隐私策略 URL**和 **应用徽标**。
1. 同意 LinkedIn **API 使用条款** ，并单击 " **创建应用**"。
1. 选择 " **身份验证** " 选项卡。在 " **身份验证密钥**" 下，复制 " **客户端 ID** " 和 " **客户端密码**" 值。 你将需要这两个将 LinkedIn 配置为租户中的标识提供者。 ****“客户端密钥”是一个很重要的安全凭据。
1. 选择 **应用的授权重定向 url**旁边的 "编辑铅笔"，然后选择 " **添加重定向 URL**"。 输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ，将替换 `your-tenant-name` 为你的租户的名称。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。 选择“更新”  。
2. 默认情况下，LinkedIn 应用未批准与登录相关的作用域。 若要请求复查，请选择 " **产品** " 选项卡，然后选择 " **使用 LinkedIn 登录**"。 评审完成后，所需范围将添加到你的应用程序。
   > [!NOTE]
   > 可以在 " **OAuth 2.0 作用域**" 部分的 "**身份验证**" 选项卡上查看应用当前允许的范围。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>将 LinkedIn 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”****，然后选择“LinkedIn”****。
1. 输入“名称”。 例如 *LinkedIn*。
1. 对于“客户端 ID”，请输入前面创建的 LinkedIn 应用程序的客户端 ID****。
1. 对于 **客户端密码**，请输入你记录的客户端密码。
1. 选择“保存”。

## <a name="migration-from-v10-to-v20"></a>从 v1.0 迁移到 v2.0

LinkedIn 最近[已将其 API 从 v1.0 更新到 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)。 在迁移过程中，Azure AD B2C 只能获取 LinkedIn 用户在注册期间提供的全名。 如果电子邮件地址是注册期间收集的属性之一，则用户必须手动输入该电子邮件地址并对其进行验证。
