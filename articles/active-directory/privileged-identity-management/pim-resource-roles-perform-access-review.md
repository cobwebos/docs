---
title: 查看 PIM 中对 Azure 资源角色的访问-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中评审 Azure 资源角色的访问权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847004"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>查看 Privileged Identity Management 中对 Azure 资源角色的访问权限

Privileged Identity Management （PIM）访问评审有助于安全访问 Azure Active Directory （Azure AD）中的特权角色。 本文介绍了完成 Azure AD 访问评审中的特权角色分配的步骤。

如果你分配到管理角色，你可能需要由管理员完成访问评审以确认你是否需要角色。 确认请求可以收到包含链接的电子邮件，也可以在[Azure 门户](https://portal.azure.com)中进行确认。

如果是特权角色管理员并且想要了解访问权限审查，请参阅[如何开始进行访问权限审查](pim-resource-roles-start-access-review.md)中的详细信息。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限

你可以根据是否仍使用此角色，批准或拒绝访问。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。 只有在审阅者应用结果后，状态才会更改。

请遵循以下步骤来查找并完成访问权限审查：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择**Azure Active Directory**并打开**Privileged Identity Management**。
1. 选择 "**查看访问**"。

   ![Privileged Identity Management 应用程序的屏幕截图，并选中 "查看访问权限" 边栏选项卡](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 选择想要完成的审查。
1. 选择 "**批准**" 或 "**拒绝**"。 如果需要，请在 "**提供原因" 框**中为决定输入业务理由。

   ![“审阅详细信息”页的屏幕截图](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中执行我的 Azure AD 角色的访问评审](pim-how-to-perform-security-review.md)
