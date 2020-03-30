---
title: 使用 Microsoft 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure 活动目录 B2C 在应用程序中使用 Microsoft 帐户向客户提供注册和登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188012"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序

要在 Azure 活动目录 B2C（Azure AD B2C）中使用 Microsoft 帐户作为[标识提供程序](openid-connect.md)，需要在 Azure AD 租户中创建应用程序。 Azure AD 租户与 Azure AD B2C 租户不同。 如果您还没有 Microsoft 帐户，则可以在 获取[https://www.live.com/](https://www.live.com/)一个帐户。

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 通过在顶部菜单中选择**目录 + 订阅**筛选器并选择包含 Azure AD 租户的目录，请确保使用的目录包含 Azure AD 租户。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择“新注册”。****
1. 输入应用程序的**名称**。 例如，MSAapp1**。
1. 在 **"支持帐户类型**"下，选择**任何组织目录中的帐户和个人 Microsoft 帐户（例如 Skype、Xbox、Outlook.com）。** 此选项针对最广泛的 Microsoft 标识集。

   有关不同帐户类型选择的详细信息，请参阅[快速入门：使用 Microsoft 标识平台注册应用程序](../active-directory/develop/quickstart-register-app.md)。
1. 在**重定向 URI（可选）** 下，选择 **"Web"** 并在文本框中输入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 替换为`your-tenant-name`Azure AD B2C 租户名称。
1. 选择 **"注册"**
1. 记录应用程序概述页上显示**的应用程序（客户端）ID。** 在下一节中配置标识提供程序时，需要这样做。
1. 选择**证书&机密**
1. 单击 **"新客户端"机密**
1. 输入机密**的描述**，例如*应用程序密码 1，* 然后单击"**添加**"。
1. 记录 **"值"** 列中显示的应用程序密码。 在下一节中配置标识提供程序时，需要这样做。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>将 Microsoft 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录****。
1. 选择 Azure 门户左上角的“所有服务”****，搜索并选择 **Azure AD B2C**。
1. 选择**标识提供程序**，然后选择**微软帐户**。
1. 输入“名称”****。 例如 *，MSA*。
1. 对于**客户端 ID**，输入前面创建的 Azure AD 应用程序的应用程序（客户端）ID。
1. 对于**客户端机密**，输入您记录的客户密钥。
1. 选择“保存”。****
