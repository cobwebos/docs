---
title: PIM 中的合格分配和资源可见性 - Azure | Microsoft Docs
description: 介绍如何在 Azure AD Privileged Identity Management (PIM) 中将成员分配为 Azure 资源角色的合格成员。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fb52bc92c86261831d0e8d8e9e863a4863fe8fb9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666883"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>PIM 中的合格分配和资源可见性

Azure 资源角色的 Privileged Identity Management (PIM) 可使具有重要 Azure 资源的组织更为安全。 资源管理员可使用 PIM 将成员分配为资源角色的合格成员。 阅读以下各部分，详细了解 Azure 资源角色的不同分配类型和分配状态。 

## <a name="assignment-types"></a>分配类型

Azure 资源的 PIM 提供了两种不同的分配类型：

- 合格
- 活动

“合格”分配要求该角色的成员执行某个操作才能使用该角色。 操作可能包括成功完成多重身份验证检查、提供业务理由或请求获得指定审批者的批准。

“活动”分配不要求成员执行任何操作便可使用该角色。 分配为“活动”的成员始终具有分配给该角色的权限。

## <a name="assignment-duration"></a>分配持续时间

为角色配置 PIM 设置时，资源管理员可以为每个分配类型从两个选项中选择一个选项。 在 PIM 中将成员分配到角色时，这些选项将成为默认的最大持续时间。 

管理员可从以下分配类型中选择一个类型：

- 允许永久的合格分配
- 允许永久的活动分配

或者，管理员可从以下分配类型中选择一个类型：

- 使合格分配在以下时间后过期
- 使活动分配在以下时间后过期

如果资源管理员选择“允许永久的合格分配”或“允许永久的活动分配”，则向该资源分配成员的所有管理员都可分配永久成员身份。

如果资源管理员选择“使合格分配在以下时间后过期”或“使活动分配在以下时间后过期”，则资源管理员可要求所有分配都具有指定开始和结束日期，以此来控制分配生命周期。

> [!NOTE] 
> 资源管理员可续订具有特定结束日期的所有分配。 此外，成员也可启动自助服务请求来[延长或续订分配](pim-resource-roles-renew-extend.md)。


## <a name="assignment-states"></a>分配状态

Azure 资源的 PIM 有两种不同的分配状态，它们显示在 PIM 的“我的角色”、“角色”和“成员”视图的“活动角色”选项卡上。 这些状态包括：

- 已分配
- 已激活

查看“活动角色”中列出的成员身份时，可使用“状态”列中的值来区分“已分配”为活动成员的用户和“已激活”了合格分配且现在处于活动状态的用户。

## <a name="azure-resource-role-approval-workflow"></a>Azure 资源角色审批工作流

借助 PIM 中 Azure 资源角色的审批工作流，管理员可进一步保护或限制对关键资源的访问。 也就是说，管理员可以要求批准以激活角色分配。

资源层次结构的概念是 Azure 资源角色的独特之处。 使用此层次结构，可将父资源对象中的角色分配向下继承到父容器中的所有子资源。 

例如，Bob（资源管理员）使用 PIM 将 Alice 作为合格成员分配到 Contoso 订阅中的“所有者”角色。 分配后，Alice 即成为该 Contoso 订阅中所有资源组容器的合格所有者。 Alice 也是该订阅每个资源组内所有资源（如虚拟机）的合格所有者。

假设 Contoso 订阅中有三个资源组：Fabrikam Test、Fabrikam Dev 和 Fabrikam Prod。其中每个资源组包含单个虚拟机。

PIM 设置是针对资源的每个角色配置的。 与分配不同，这些设置不是继承的，且会严格应用到资源角色。

继续沿用该示例：Bob 使用 PIM 来要求 Contoso 订阅“所有者”角色中的所有成员请求激活审批。 为帮助保护 Fabrikam Prod 资源组中的资源，Bob 还要求审批此资源的“所有者”角色的成员。 Fabrikam Test 和 Fabrikam Dev 中的“所有者”角色不需要请求激活审批。

当 Alice 请求激活 Contoso 订阅的“所有者”角色时，审批者必须在 Alice 激活角色之前批准或拒绝她的请求。 如果 Alice 决定[将激活范围限定为](pim-resource-roles-activate-your-roles.md) Fabrikam Prod 资源组，则审批者也必须批准或拒绝此请求。 但如果 Alice 决定将激活范围限定为 Fabrikam Test 和/或 Fabrikam Dev，则不需要审批。

不一定要对所有角色成员执行审批工作流。 假设你的组织招聘了几名合同工来帮助开发将在 Azure 订阅中运行的应用程序。 作为资源管理员，你希望正式员工可以在不需要审批的情况下获得合格访问权，但合同工必须请求审批。 如果只是为合同工配置审批工作流，则可创建一个自定义角色，该角色的权限与分配给正式员工的角色所拥有的权限相同。 你可以要求批准以激活该自定义角色。 [详细了解自定义角色](pim-resource-roles-custom-role-policy.md)。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
