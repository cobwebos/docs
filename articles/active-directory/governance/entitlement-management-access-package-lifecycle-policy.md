---
title: 更改 Azure AD 授权管理中访问包的生命周期设置 - Azure 活动目录
description: 了解如何在 Azure 活动目录授权管理中更改访问包的生命周期设置。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261978"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>更改 Azure AD 授权管理中访问包的生命周期设置

作为访问包管理器，您可以随时通过编辑现有策略来更改访问包的生命周期设置。 如果更改了策略的过期日期，已处于“等待审批”或“已审批”状态的请求的过期日期不会更改。

本文介绍如何更改现有访问包的生命周期设置。

## <a name="open-lifecycle-settings"></a>开放生命周期设置

要更改访问包的生命周期设置，您需要打开相应的策略。 按照以下步骤打开访问包的生命周期设置。

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击 **"策略"，** 然后单击具有要编辑的生命周期设置的策略。

    页面底部会打开“策略详细信息”窗格。

    ![访问包 -“策略详细信息”窗格](./media/entitlement-management-shared/policy-details.png)

1. 单击“编辑”以编辑策略。****

    ![访问包 - 编辑策略](./media/entitlement-management-shared/policy-edit.png)

1. 单击 **"生命周期**"选项卡以打开生命周期设置。

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>后续步骤

- [更改访问包的请求和审批设置](entitlement-management-access-package-request-policy.md)