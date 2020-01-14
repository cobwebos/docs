---
title: 使用 Privileged Identity Management Azure Active Directory 的许可要求 |Microsoft Docs
description: 介绍使用 Azure AD Privileged Identity Management (PIM) 所要满足的许可要求。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932322"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>要使用的许可证要求 Privileged Identity Management

若要使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，目录必须具有有效的许可证。 此外，必须将许可证分配给管理员和相关用户。 本文介绍使用 Privileged Identity Management 的许可要求。

## <a name="valid-licenses"></a>有效的许可证

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>必须拥有多少个许可证？

确保你的目录的 Azure AD Premium P2 许可证至少与将执行以下任务的员工一样多：

- 分配有资格使用 PIM 管理 Azure AD 角色的用户
- 用户能够在 PIM 中批准或拒绝激活请求
- 已通过实时或直接（基于时间）分配方法分配到 Azure 资源角色的用户  
- 已分配到访问评审的用户
- 执行访问评审的用户

以下任务**不**需要 Azure AD Premium P2 许可证：

- 使用全局管理员或特权角色管理员角色（设置 PIM、配置策略、接收警报和设置访问评审）的用户不需要许可证。

有关许可证的详细信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。

## <a name="example-license-scenarios"></a>示例许可证方案

下面是一些示例许可方案，可帮助您确定您必须拥有的许可证数量。

| 方案 | 计算 | 许可证数量 |
| --- | --- | --- |
| Woodgrove Bank 包含用于不同部门的10个管理员和用于配置和管理 PIM 的2个全局管理员。 它们使五个管理员有资格。 | 适用于符合条件的管理员的五个许可证 | 5 |
| 图形设计研究所有25个管理员，其中14个通过 PIM 管理。 角色激活需要批准，组织中有三个可批准激活的用户。 | 14个适用于合格角色的许可证 + 三个审批者 | 17 |
| Contoso 有50个管理员，其中42通过 PIM 进行管理。 角色激活需要批准，组织中有5个不同的用户可批准激活。 Contoso 还按月审查分配给管理员角色和审阅者的用户，这是其六个用户的经理，而不是由 PIM 管理的管理员角色。 | 适用于符合条件的角色的42许可证 + 五个审批者 + 六个审阅者 | 53 |

## <a name="what-happens-when-a-license-expires"></a>许可证过期时会发生什么情况？

如果 Azure AD Premium P2、EMS E5 或试用版许可证已过期，则目录中将不再提供 Privileged Identity Management 功能：

- 对 Azure AD 角色的永久角色分配会受到影响。
- Azure 门户中的 Privileged Identity Management 服务以及 Privileged Identity Management 的图形 API cmdlet 和 PowerShell 接口将不再可供用户激活特权角色、管理特权访问或执行特权角色的访问评审。
- 将删除 Azure AD 角色的符合条件的角色分配，因为用户不再能够激活特权角色。
- Azure AD 角色的任何正在进行的访问评审都将结束，并且将删除 Privileged Identity Management 的配置设置。
- Privileged Identity Management 将不再发送有关角色分配更改的电子邮件。

## <a name="next-steps"></a>后续步骤

- [部署 Privileged Identity Management](pim-deployment-plan.md)
- [开始使用 Privileged Identity Management](pim-getting-started.md)
- [在 Privileged Identity Management 中无法管理的角色](pim-roles.md)
