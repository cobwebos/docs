---
title: Privileged Identity Management 中针对 Azure 的合格分配和资源可见性 | Microsoft Docs
description: 介绍如何在使用 PIM 时将成员分配为资源角色的合格成员。
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
ms.openlocfilehash: 336453c1ef6ef8d0295d00f31afc6a5e7e42e8b6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621542"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>使用 Privileged Identity Management 实现合格分配和资源可见性

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

## <a name="next-steps"></a>后续步骤

[在 Privileged Identity Manager 中分配角色](pim-resource-roles-assign-roles.md)
