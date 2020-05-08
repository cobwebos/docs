---
title: 配置会话行为 - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中配置会话行为。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f5400b47c1e0b4657e40d2c57f8212711bbdaf3f
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927065"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置会话行为

Azure Active Directory B2C （Azure AD B2C）中的[单一登录（SSO）会话](session-overview.md)管理使管理员能够在用户已经过身份验证后控制与用户的交互。 例如，管理员可以控制是否显示所选的标识提供者，或者是否需要再次输入帐户详细信息。 本文介绍如何配置 Azure AD B2C SSO 的设置。

## <a name="session-behavior-properties"></a>会话行为属性

可使用以下属性来管理 Web 应用程序会话：

- **Web 应用会话生存期（分钟）** - 身份验证成功后，存储在用户浏览器上的 Azure AD B2C 会话 Cookie 的生存期。
    - 默认值 = 1440 分钟。
    - 最小值（含）= 15 分钟。
    - 最大值（含）= 1440 分钟。
- **Web 应用会话超时**-[会话过期类型](session-overview.md#session-expiry-type)、*滚动*或*绝对*。 
- **单一登录配置**-Azure AD B2C 租户中跨多个应用和用户流的单一登录（SSO）行为的[会话范围](session-overview.md#session-scope)。 


## <a name="configure-the-properties"></a>配置属性

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录****。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
4. 选择“用户流(策略)”****。
5. 打开之前创建的用户流。
6. 选择“属性”  。
7. 根据需要配置 **Web 应用会话生存期（分钟）**、**Web 应用会话超时**、**单一登录配置**和**在注销请求中需要 ID 令牌**。

    ![Azure 门户中的会话行为属性设置](./media/session-behavior/session-behavior.png)

8. 单击“保存”  。

## <a name="next-steps"></a>后续步骤

- 了解[Azure AD B2C 会话](session-overview.md)的详细信息。