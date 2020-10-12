---
title: 在 PIM 中评审 Azure 资源角色的访问权限 - Azure AD | Microsoft Docs
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
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743671"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中评审对 Azure 资源角色的访问权限

Privileged Identity Management (PIM) 访问评审有助于安全访问 Azure Active Directory (Azure AD) 中的特权角色。 本文介绍了在 Azure AD 访问评审中完成特权角色分配评审的步骤。

如果已为你分配了管理角色，可能需要由管理员来完成你的访问评审，以确认你是否需要某个角色。 可以在包含链接的电子邮件中完成确认请求中的确认，也可以在 [Azure 门户](https://portal.azure.com)中进行确认。

如果是特权角色管理员并且想要了解访问权限审查，请参阅[如何开始进行访问权限审查](pim-resource-roles-start-access-review.md)中的详细信息。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限

你可以根据自己是否仍使用此角色来批准或拒绝访问。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。   你的状态仅在审阅者应用结果后才会更改。

请遵循以下步骤来查找并完成访问权限审查：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 选择“Azure Active Directory”，打开“Privileged Identity Management”。  
1. 选择“评审访问权限”。 

   ![Privileged Identity Management 应用程序屏幕截图，选中了“审阅访问权限”边栏选项卡](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 选择想要完成的审查。
1. 选择“批准”或“拒绝”。   在“提供原因”框中，必要时输入进行决策的业务理由。 

   ![“审阅详细信息”页的屏幕截图](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中执行对我的 Azure AD 角色的访问评审](pim-how-to-perform-security-review.md)
