---
title: 设置 Weibo 帐户的注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过微博帐户注册与登录的功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f84d1d33874ac70a21c9d596c6fa5a9e608bb84
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950300"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过微博帐户注册与登录

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>创建 Weibo 应用程序

若要在 Azure Active Directory B2C （Azure AD B2C）中使用 Weibo 帐户作为标识提供者，需要在租户中创建表示它的应用程序。 如果还没有 Weibo 帐户，可以[https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)进行注册。

1. 使用微博帐户凭据登录[微博开发人员门户](https://open.weibo.com/)。
1. 登录后，选择右上角的显示名称。
1. 在下拉列表中，选择“编辑开发者信息”(edit developer information)。
1. 输入所需的信息，并选择“提交” (submit)。
1. 完成电子邮件验证过程。
1. 转到[“身份验证”页](https://open.weibo.com/developers/identity/edit)。
1. 输入所需的信息，并选择“提交” (submit)。

### <a name="register-a-weibo-application"></a>注册 Weibo 应用程序

1. 转到[“新 Weibo 应用注册”页](https://open.weibo.com/apps/new)。
1. 输入所需的应用程序信息。
1. 选择“创建”(create)。
1. 复制**应用密钥**和**应用机密**的值。 将标识提供者添加到租户时需要这两项。
1. 上传所需的照片，并输入所需的信息。
1. 选择“保存以上信息”(save)。
1. 选择“高级信息”(advanced information)。
1. 选择 OAuth2.0“授权设置”(redirect URL) 字段旁边的“编辑”(edit)。
1. 为 OAuth2.0“授权设置”(redirect URL) 输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 例如，如果租户名称是 contoso，请将 URL 设置为 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 选择“提交”(submit)。

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>将微博帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**"，然后选择 " **Weibo （预览版）** "。
1. 输入“名称”。 例如， *Weibo*。
1. 对于 "**客户端 ID**"，请输入之前创建的 Weibo 应用程序的应用密钥。
1. 对于**客户端密码**，请输入你记录的应用密码。
1. 选择“保存”。
