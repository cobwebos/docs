---
title: 设置通过 Microsoft 帐户注册与登录 - Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Microsoft 帐户注册与登录的功能。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d6557d8dccd3c61307bc3d29c86d98722d793170
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703772"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序

若要在 Azure Active Directory (Azure AD) B2C 中将 Microsoft 帐户用作[标识提供者](active-directory-b2c-reference-oidc.md)，则需要在代表它的租户中创建应用程序。 如果还没有 Microsoft 帐户，可以在 [https://www.live.com/](https://www.live.com/) 获取。

1. 使用 Microsoft 帐户凭据登录 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。
2. 在右上角，选择“添加应用”。
3. 输入应用程序的**名称**。 例如，MSAapp1。
4. 选择“生成新密码”，并确保复制密码以在配置身份提供程序时使用。 还要复制“应用程序 ID”。 
5. 选择“添加平台”，然后选择“Web”。
4. 在“重定向 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。
5. 选择“保存”。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>将 Microsoft 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”，然后选择“添加”。
5. 提供“名称”。 例如，输入“MSA”。
6. 选择“标识提供者类型”，选择“Microsoft 帐户”，并单击“确定”。
7. 选择“设置此标识提供者”，然后输入你之前记录为“客户端 ID”的应用程序 ID，并输入你记录为之前创建的 Microsoft 帐户应用程序的“客户端机密”的密码。
8. 单击“确定”，并单击“创建”以保存 Microsoft 帐户配置。

