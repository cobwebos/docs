---
title: 为 Azure AD 组织设置登录
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678039"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织

若要将 Azure Active Directory (Azure AD) 用作 Azure AD B2C 中的[标识提供者](authorization-code-flow.md)，需要创建一个表示它的应用程序。 本文介绍如何使用 Azure AD B2C 中的用户流为特定 Azure AD 组织中的用户启用登录。

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>将 Azure AD 配置为标识提供者

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录****。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 选择“标识提供者”****，然后选择“新建 OpenID Connect 提供程序”****。
1. 输入**名称**。 例如，输入“Contoso Azure AD”**。
1. 对于**元数据 URL，** 输入以下 URL`{tenant}`替换为 Azure AD 租户的域名：

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    例如，`https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` 。

1. 对于“客户端 ID”，请输入之前记录的应用程序 ID****。
1. 对于“客户端机密”，请输入之前记录的客户端机密****。
1. 对于**作用域**，输入 。 `openid profile`
1. 保留**响应类型**和**响应模式**的默认值。
1. （可选）对于**域提示**，请输入`contoso.com`。 有关详细信息，请参阅[使用 Azure Active Directory B2C 设置直接登录](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在 **"标识提供程序声明映射"下**，选择以下声明：

    * **用户 ID**：*oid*
    * **显示名称**：*name*
    * **名字**：*given_name*
    * **姓氏**：*family_name*
    * **电子邮件**：*unique_name*

1. 选择“保存”。 
