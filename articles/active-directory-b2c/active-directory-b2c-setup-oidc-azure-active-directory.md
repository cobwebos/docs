---
title: Azure Active Directory B2C：使用内置策略添加 Azure AD 提供程序 | Microsoft Docs
description: 了解如何添加 Open ID 连接标识提供者 (Azure AD)
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 7dac9545-d5f1-4136-a04d-1c5740aea499
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/27/2018
ms.author: parja
ms.openlocfilehash: 52a752df9cf199acf39596f49e7368bce27a8158
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2018
ms.locfileid: "32309012"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C：通过内置策略使用 Azure AD 帐户登录

>[!NOTE]
> 此功能目前以公共预览版提供。 请勿在生产环境中使用该功能。

本文介绍如何让用户使用特定的 Azure Active Directory (Azure AD) 组织内置策略登录。

## <a name="create-an-azure-ad-app"></a>创建 Azure AD 应用

若要让用户从特定的 Azure AD 组织登录，需要在组织 Azure AD 租户中注册一个应用程序。

>[!NOTE]
> 我们在以下说明中将“contoso.com”用于组织 Azure AD 租户，并将“fabrikamb2c.onmicrosoft.com”用作 Azure AD B2C 租户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部栏中选择帐户。 在“目录”列表中，选择要注册应用程序的组织 Azure AD 租户 (contoso.com)。
1. 在左侧窗格中选择“所有服务”并搜索“应用注册”。
1. 选择“新建应用程序注册”。
1. 输入应用程序的名称（例如 `Azure AD B2C App`）。
1. 选择“Web 应用/API”作为应用程序类型。
1. 对于“登录 URL”，输入以下 URL，其中 `yourtenant` 替换为 Azure AD B2C 租户的名称 (`fabrikamb2c.onmicrosoft.com`)：

    >[!NOTE]
    >“登录 URL”中“yourtenant”的值必须全小写。

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. 保存应用程序 ID，下一部分会以此作为客户端 ID。
1. 在“设置”边栏选项卡下，选择“密钥”。
1. 在“密码”部分下输入“密钥描述”并将“持续时间”设为“永不过期”。 
1. 单击“保存”并记下生成的密钥值，下一部分会以此作为客户端密码。

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>将 Azure AD 配置为租户中的标识提供者

1. 在顶部栏中选择帐户。 在“目录”列表中选择 Active Directory B2C 租户 (fabrikamb2c.onmicrosoft.com).
1. 在 Azure 门户中[导航到 Active Directory B2C 设置菜单](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
1. 在 Active Directory B2C 设置菜单中单击“标识提供者”。
1. 单击边栏选项卡顶部的“ **+添加** ”。
1. 为标识提供者配置提供一个友好**名称**。 例如，输入“Contoso Azure AD”。
1. 单击“标识提供者类型”，选择“Open ID 连接”并单击“确定”。
1. 单击“设置此标识提供者”
1. 在“元数据 URL”中输入以下 URL，并将其中的 `yourtenant` 替换为 Azure AD 租户的名称（例如 `contoso.com`）：

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. 在“客户端 ID”和“客户端密码”中输入前一部分中的应用程序 ID 和密钥值。
1. 保留“作用域”的默认值，即 `openid`。
1. 保留“响应类型”的默认值，即 `code`。
1. 保留“响应模式”的默认值，即 `form_post`。
1. 输入“域”值，例如 `ContosoAD`（可选操作）。 在请求中使用 domain_hint 引用此标识提供者时会用到该值。 
1. 单击“确定”。
1. 单击“映射此标识提供程序的声明”。
1. 在“用户 ID”中输入 `oid`。
1. 在“显示名称”中输入 `name`。
1. 在“名字”中输入 `given_name`。
1. 在“姓氏”中输入 `family_name`。
1. 在“电子邮件”中输入 `unique_name`
1. 单击“确定”和“创建”以保存配置。

## <a name="next-steps"></a>后续步骤

将新创建的 Azure AD 标识提供者添加到内置策略并将反馈发送至 [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)。
