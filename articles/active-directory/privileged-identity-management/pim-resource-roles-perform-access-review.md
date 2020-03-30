---
title: 查看对 PIM 中 Azure 资源角色的访问 - Azure AD |微软文档
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847004"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>查看对特权标识管理中的 Azure 资源角色的访问

特权标识管理 （PIM） 访问审核可帮助保护对 Azure 活动目录 （Azure AD） 中特权角色的访问。 本文介绍在 Azure AD 访问审阅中完成审核特权角色分配的步骤。

如果您被分配到管理角色，则可能需要管理员完成访问审核，以确认是否需要角色。 确认请求可以附带包含链接的电子邮件，也可以在[Azure 门户](https://portal.azure.com)中进行确认。

如果是特权角色管理员并且想要了解访问权限审查，请参阅[如何开始进行访问权限审查](pim-resource-roles-start-access-review.md)中的详细信息。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限

您可以根据是否仍使用此角色来批准或拒绝访问权限。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。******** 只有在审阅者应用结果后，您的状态才会更改。

请遵循以下步骤来查找并完成访问权限审查：

1. 登录到 Azure[门户](https://portal.azure.com/)。
1. 选择**Azure 活动目录**并打开**特权标识管理**。
1. 选择 **"审阅访问权限**"。

   ![特权身份管理应用程序的屏幕截图，并选择了查看访问边栏选项卡](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 选择想要完成的审查。
1. 选择 **"批准**"或 **"拒绝**"。 在 **"提供原因"框中**，根据需要输入决策的业务理由。

   ![“审阅详细信息”页的屏幕截图](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>后续步骤

- [在特权标识管理中对 Azure AD 角色执行访问审查](pim-how-to-perform-security-review.md)
