---
title: 设置使用 Microsoft 帐户的注册和登录-Azure Active Directory B2C
description: 使用 Azure Active Directory B2C 向客户提供应用程序中的 Microsoft 帐户的注册和登录。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a921d6110fd0294012e2e59542ba2c82d63ffcde
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642076"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序

若要将 Microsoft 帐户用作 Azure Active Directory B2C （Azure AD B2C）中的[标识提供者](active-directory-b2c-reference-oidc.md)，需要在 Azure AD 租户中创建应用程序。 Azure AD 租户与 Azure AD B2C 租户不同。 如果还没有 Microsoft 帐户，可以在[https://www.live.com/](https://www.live.com/)获取一个。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含 Azure AD 租户的目录，确保使用的是包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”。
1. 选择“新注册”。
1. 输入应用程序的**名称**。 例如，MSAapp1。
1. 在 "**受支持的帐户类型**" 下，选择**任何组织目录中的帐户和个人 Microsoft 帐户（例如 Skype、Xbox、Outlook.com）** 。 此选项针对最广泛的 Microsoft 标识。

   有关不同的帐户类型选择的详细信息，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。
1. 在 "**重定向 URI （可选）** " 下，选择 " **Web** "，然后在文本框中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为 Azure AD B2C 的租户名称。
1. 选择**注册**
1. 记录应用程序概述页上显示的**应用程序（客户端） ID** 。 在下一部分中配置标识提供者时需要此项。
1. 选择**证书 & 机密**
1. 单击 "**新建客户端密码**"
1. 输入机密的**说明**，例如 "*应用程序密码 1*"，然后单击 "**添加**"。
1. 记录 "**值**" 列中显示的应用程序密码。 在下一部分中配置标识提供者时需要此项。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>将 Microsoft 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**"，然后选择 " **Microsoft 帐户**"。
1. 输入“名称”。 例如， *MSA*。
1. 对于 "**客户端 ID**"，请输入之前创建的 Azure AD 应用程序的应用程序（客户端） id。
1. 对于**客户端密码**，请输入你记录的客户端密码。
1. 选择“保存”。
