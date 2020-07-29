---
title: 在 Azure AD 授权管理-Azure Active Directory 中更改访问包的生命周期设置
description: 了解如何在 Azure Active Directory 权限管理中更改访问包的生命周期设置。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 538ed31c1f3bbca8c2151a6ac0d2a3ad561bc6a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078783"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权限管理中更改访问包的生命周期设置

作为访问包管理器，你可以通过编辑现有策略，随时更改访问包的生命周期设置。 如果更改了策略的过期日期，已处于“等待审批”或“已审批”状态的请求的过期日期不会更改。

本文介绍如何更改现有访问包的生命周期设置。

## <a name="open-lifecycle-settings"></a>打开生命周期设置

若要更改访问包的生命周期设置，需要打开相应的策略。 按照以下步骤打开访问包的生命周期设置。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击 "**策略**"，然后单击具有要编辑的生命周期设置的策略。

    页面底部会打开“策略详细信息”窗格。

    ![访问包 -“策略详细信息”窗格](./media/entitlement-management-shared/policy-details.png)

1. 单击“编辑”以编辑策略。****

    ![访问包 - 编辑策略](./media/entitlement-management-shared/policy-edit.png)

1. 单击 "**生命周期**" 选项卡以打开生命周期设置。

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>后续步骤

- [更改访问包的请求和审批设置](entitlement-management-access-package-request-policy.md)