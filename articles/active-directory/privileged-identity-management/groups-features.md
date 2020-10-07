---
title: 在 Privileged Identity Management (PIM) 中管理特权 Azure AD 组 | Microsoft Docs
description: 如何在 Privileged Identity Management (PIM) 中管理特权访问组的成员和所有者
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b6709d7589ebf4c82ec25c6b7a79fa31a305839
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87506437"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>特权访问 Azure AD 组的管理功能（预览版）

在 Privileged Identity Management (PIM) 中，现在可以针对特权访问组的成员资格或所有权分配资格。 从此预览版开始，可以将 Azure Active Directory (Azure AD) 内置角色分配给云组，并使用 PIM 管理组成员和所有者资格和激活。 有关 Azure AD 中可分配角色的组的详细信息，请参阅[在 Azure Active Directory 中使用云组管理角色分配（预览版）](../users-groups-roles/roles-groups-concept.md)。

## <a name="require-different-policies-for-each-role-assignable-group"></a>每个可分配角色的组需要不同的策略

某些组织使用 Azure AD 企业到企业 (B2B) 协作之类的工具邀请其合作伙伴作为来宾加入其 Azure AD 组织。 可以使用创建两个不同的特权访问组及其自己的策略，而不是为针对某个特权角色的所有分配使用单一的实时策略。 可以对受信任员工实施不太严格的要求，并在合作伙伴请求激活到其分配的组时对他们实施更严格的要求。

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>在单个请求中激活多个角色分配

借助特权访问组预览版，可以为特定于工作负载的管理员提供通过单个实时请求快速访问多个角色的权限。 例如，第 3 层 Office 管理员可能需要实时访问 Exchange 管理员、Office 应用管理员、Teams 管理员和搜索管理员角色，以便每天彻底调查事件。 今天之前这需要四个连续请求，是一个需要花费一些时间的过程。 相反，可以创建一个名为“第 3 层 Office 管理员”的可分配角色的组，将它分配给前面提到的所有四个角色（或任何 Azure AD 内置角色），并在该组的“活动”部分中为特权访问启用相应角色。 为特权访问启用后，可以为组成员配置实时设置，并将管理员和所有者分配为符合条件。 当管理员提升到组中时，他们会成为所有四个 Azure AD 角色的成员。

## <a name="extend-and-renew-group-assignments"></a>扩展和续订组分配

在设置了受时间限制的所有者或成员分配之后，你可能提出的第一个问题是在分配到期后会出现什么情况？ 在此新版本中，我们为此情形提供了两个选项：

- 延期 – 当角色分配接近到期时，用户可以使用 Privileged Identity Management 请求对角色分配进行延期
- 续订 – 当角色分配已到期时，用户可以使用 Privileged Identity Management 请求对角色分配进行续订

这两种用户启动的操作都需要全局管理员或特权角色管理员批准。 管理员不再需要负责管理这些到期情况。 他们可以只需等待延期或续订请求，并在请求有效时批准它们。

## <a name="next-steps"></a>后续步骤

- [分配特权访问组所有者或成员](groups-assign-member-owner.md)
- [批准或拒绝特权访问组成员和所有者的激活请求](groups-approval-workflow.md)
