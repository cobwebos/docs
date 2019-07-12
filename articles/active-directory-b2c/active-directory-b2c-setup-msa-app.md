---
title: 注册和使用 Microsoft 帐户-Azure Active Directory B2C 登录设置
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Microsoft 帐户注册与登录的功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 82c1be335bfd39d641f0203116e68a4cb4c0a674
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654215"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Microsoft 帐户注册与登录

## <a name="create-a-microsoft-account-application"></a>创建 Microsoft 帐户应用程序

若要使用 Microsoft 帐户作为[标识提供者](active-directory-b2c-reference-oidc.md)需要在 Azure Active Directory (Azure AD) B2C 中，Azure AD 租户中创建应用程序。 Azure AD 租户与 Azure AD B2C 租户不同。 如果还没有 Microsoft 帐户，可以获得一个在[ https://www.live.com/ ](https://www.live.com/)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请务必使用包含 Azure AD 租户的目录，具体方法是：单击顶部菜单中的“目录和订阅筛选器”  ，再选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”   。
1. 选择“新注册”。 
1. 输入应用程序的**名称**。 例如，MSAapp1  。
1. 下**支持的帐户类型**，选择**中的任何组织的目录和个人 Microsoft 帐户 (例如 Skype，Xbox、 Outlook.com) 帐户**。 此选项以尽可能最广泛的 Microsoft 标识集为目标。

   有关不同的帐户类型选择的详细信息，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。
1. 下 **（可选） 重定向 URI**，选择**Web**并输入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`在文本框中。 替换为`your-tenant-name`与 Azure AD B2C 租户的名称。
1. 选择**注册**
1. 记录**应用程序 （客户端） ID**的应用程序概述页上显示。 下一节中配置标识提供者时需要此。
1. 选择**证书和机密**
1. 单击**新客户端机密**
1. 输入**描述**机密，例如*应用程序密码 1*，然后单击**添加**。
1. 记录应用程序密码中所示**值**列。 下一节中配置标识提供者时需要此。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>将 Microsoft 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
1. 选择“标识提供者”  ，然后选择“添加”  。
1. 提供“名称”  。 例如，输入“MSA”  。
1. 选择“标识提供者类型”  ，选择“Microsoft 帐户”  ，并单击“确定”  。
1. 选择**设置此标识提供者**并输入之前在记录的应用程序 （客户端） ID**客户端 ID**文本，并输入你在中记录的客户端机密**客户端机密**文本框。
1. 单击“确定”  ，并单击“创建”  以保存 Microsoft 帐户配置。
