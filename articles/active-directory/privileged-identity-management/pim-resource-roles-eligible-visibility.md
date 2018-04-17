---
title: Azure 资源的 Privileged Identity Management - 合格分配和资源可见性 | Microsoft Docs
description: 介绍了如何将成员分配为资源角色的合格成员。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>合格分配和资源可见性

Azure 资源的 PIM 中的角色为具有重要 Azure 资源的组织提供了增强的安全性。 PIM 为资源管理员提供了将成员分配为资源角色的合格成员的能力。 请阅读下文中有关 Azure 资源角色的不同分配类型和状态的更多内容。 

## <a name="assignment-types"></a>分配类型

Azure 资源的 PIM 提供了两种不同的分配类型：

- 合格
- 活动

“合格”分配要求该角色的成员执行某个操作才能使用该角色。 这些操作可能包括成功完成多重身份验证检查、提供理由以及请求获得指定审批者的批准。

“活动”分配不要求该角色的成员执行任何操作便可使用该角色。 分配为“活动”的成员始终具有该角色提供的所有权限。

## <a name="assignment-duration"></a>分配持续时间

在角色上配置 PIM 设置时，资源管理员可以为每个分配类型选择两个选项之一。 在 PIM 中将成员分配到角色时，这些选项将成为默认的最大持续时间。

- 允许永久的合格分配
- 允许永久的活动分配

或

- 使合格分配在以下时间后过期
- 使活动分配在以下时间后过期

如果资源管理员选择“允许永久的合格分配”和/或“允许永久的活动分配”，则向该资源分配成员的所有管理员都将能够分配永久成员身份。

选择“使合格分配在以下时间后过期”和/或“使活动分配在以下时间后过期”将能够通过要求所有分配都具有指定的开始和结束日期来控制分配生命周期。

>[!NOTE] 
>资源管理员可以续订具有指定结束日期的所有分配，成员可以发出自助服务请求来[延长或续订分配](pim-resource-roles-renew-extend.md)。


## <a name="assignment-states"></a>分配状态

Azure 资源的 PIM 有两种不同的分配状态，它们显示在 PIM 的“我的角色”、“角色”和“成员”视图的“活动角色”选项卡上。 这些状态包括：

- 已分配
- 已激活

查看“活动角色”中列出的成员身份时，可以通过“状态”列来区分“已分配”为活动成员的用户与“已激活”了合格分配且现在处于活动状态的用户。

## <a name="next-steps"></a>后续步骤

[在 PIM 中分配角色](pim-resource-roles-assign-roles.md)

