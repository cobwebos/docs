---
title: 配置令牌 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中配置令牌生存期和兼容性设置。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2b9cc0c69f0c559c9561a02de4192974ee7f521
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926181"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置令牌

本文介绍如何在 Azure Active Directory B2C (Azure AD B2C) 中配置[令牌的生存期和兼容性](tokens-overview.md)。

## <a name="prerequisites"></a>必备条件

[创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。

## <a name="configure-jwt-token-lifetime"></a>配置 JWT 令牌生存期

可以在任何用户流上配置令牌生存期。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
4. 选择“用户流(策略)”  。
5. 打开之前创建的用户流。
6. 选择“属性”  。
7. 在“令牌生存期”  下，调整以下属性以满足应用程序的需要：

    ![Azure 门户中的令牌生存期属性设置](./media/configure-tokens/token-lifetime.png)

8. 单击“保存”  。

## <a name="configure-jwt-token-compatibility"></a>配置 JWT 令牌兼容性

1. 选择“用户流(策略)”  。
2. 打开之前创建的用户流。
3. 选择“属性”  。
4. 在“令牌兼容性设置”  下，调整以下属性以满足应用程序的需要：

    ![Azure 门户中的令牌兼容性属性设置](./media/configure-tokens/token-compatibility.png)

5. 单击“保存”  。

## <a name="next-steps"></a>后续步骤

详细了解如何[使用访问令牌](access-tokens.md)。



