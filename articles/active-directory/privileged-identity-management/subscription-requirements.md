---
title: 使用 Privileged Identity Management 所要满足的许可证要求 - Azure Active Directory | Microsoft Docs
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
ms.topic: how-to
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c9cd1c55f1b0dde173a7ffbeac92e5518db81e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005796"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>使用 Privileged Identity Management 所要满足的许可证要求

若要使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，目录必须具有有效的许可证。 此外，必须将许可证分配给管理员和相关用户。 本文介绍使用 Privileged Identity Management 所要满足的许可证要求。

## <a name="valid-licenses"></a>有效的许可证

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="licenses-you-must-have"></a>你必须拥有的许可证

确保你的目录具有的 Azure AD Premium P2 许可证至少与将执行以下任务的员工一样多：

- 已分配到使用 PIM 管理的 Azure AD 或 Azure 角色的合格用户
- 被分配为特权访问组的符合条件的成员或所有者的用户
- 能够在 PIM 中批准或拒绝请求的用户
- 已分配到访问评审的用户
- 执行访问评审的用户

以下任务无需 Azure AD Premium P2 许可证：

- 设置 PIM、配置策略、接收警报和设置访问评审的用户不需要许可证。

有关许可证的详细信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。

## <a name="example-license-scenarios"></a>许可证场景示例

下面是一些许可证场景示例，可帮助你确定必须拥有的许可证数量。

| 方案 | 计算 | 许可证数量 |
| --- | --- | --- |
| Woodgrove Bank 有用于不同部门的 10 个管理员和用于配置和管理 PIM 的 2 个全局管理员。 他们使五个管理员符合条件。 | 五个许可证用于符合条件的管理员 | 5 |
| Graphic Design Institute 有 25 个管理员，其中 14 个是通过 PIM 管理的。 角色激活需要批准，并且组织中有三个不同的用户可以批准激活。 | 14 个许可证用于符合条件的角色 + 三个审批者 | 17 |
| Contoso 有 50 个管理员，其中 42 个是通过 PIM 管理的。 角色激活需要批准，并且组织中有五个不同的用户可以批准激活。 Contoso 还每月对分配给管理员角色的用户进行一次审阅，审阅者是用户的经理，其中有六个不在 PIM 管理的管理员角色中。 | 42 个许可证用于符合条件的角色 + 五个审批者 + 六个审阅者 | 53 |

## <a name="when-a-license-expires"></a>当许可证到期时

如果 Azure AD Premium P2、EMS E5 或试用许可证过期，则不再可以在目录中使用 Privileged Identity Management 功能：

- 对 Azure AD 角色的永久角色分配会受到影响。
- Azure 门户中的 Privileged Identity Management 服务，以及 Graph API cmdlet 和 Privileged Identity Management 的 PowerShell 接口不再可供用户用来激活特权角色、管理特权访问或执行特权角色的访问评审。
- 将删除 Azure AD 角色的符合条件的角色分配，因为用户不再能够激活特权角色。
- Azure AD 角色的任何正在进行的访问评审将结束，并且将删除 Privileged Identity Management 配置设置。
- 角色分配更改时，Privileged Identity Management 将不再发送电子邮件。

## <a name="next-steps"></a>后续步骤

- [部署 Privileged Identity Management](pim-deployment-plan.md)
- [开始使用 Privileged Identity Management](pim-getting-started.md)
- [无法在 Privileged Identity Management 中管理的角色](pim-roles.md)
