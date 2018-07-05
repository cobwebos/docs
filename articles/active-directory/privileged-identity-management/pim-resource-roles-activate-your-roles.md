---
title: 使用 Privileged Identity Management 激活 Azure 资源的角色 | Microsoft Docs
description: 介绍如何在 PIM 中激活角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d2f61f1ebdd473a24115c7774801f5b7694f224f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443199"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 激活 Azure 资源的角色
Privileged Identity Management (PIM) 引入了激活 Azure 资源角色的新体验。 符合条件的角色成员可计划在未来某个时间激活角色。 他们还可选择特定激活持续时间，但不能超过最长持续时间（由管理员配置）。 有关详细信息，请参阅[如何在 Azure AD Privileged Identity Management 中激活或停用角色](../active-directory-privileged-identity-management-how-to-activate-role.md)。

## <a name="activate-roles"></a>激活角色
转到左侧窗格中“我的角色”部分。 针对要激活的角色，选择“激活”。

![“我的角色”窗格中“符合条件的角色”选项卡。](media/azure-pim-resource-rbac/rbac-roles.png)

在“激活”菜单中输入激活角色的开始日期和时间。 可根据需要缩短激活持续时间（角色保持活动状态的时长），并根据需要输入理由。 然后选择“激活”。

如果未修改开始日期和时间，角色在几秒内即会激活。 在“我的角色”窗格中，横幅消息显示某个角色已排队等待激活。 选择刷新按钮可清除此消息。

![显示横幅信息和待批准通知的“我的角色”窗格](media/azure-pim-resource-rbac/rbac-activate-notification.png)

如果计划在将来的某个时间进行激活，挂起的请求会显示在左侧窗格的“挂起的请求”选项卡中。 如果不在需要角色激活，则可选择“取消”按钮，取消该请求。

![带“取消”按钮的挂起的请求列表](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>应用 Just Enough Administration 做法

在适用于 Azure 资源的 PIM 中，可以轻松针对资源角色分配使用 Just Enough Administration (JEA) 最佳做法。 Azure 订阅或资源组中获得分配的用户和组成员可以在更小的范围内激活其现有角色分配。 

在搜索页面中，找到需要管理的附属资源。

![选择资源](media/azure-pim-resource-rbac/azure-resources-02.png)

在左侧窗格中选择“我的角色”，并选择要激活的相应角色。 分配类型为“继承”，因为角色是在订阅级别而不是资源组级别分配的。

![符合条件的角色分配列表，其中突出显示了分配类型](media/azure-pim-resource-rbac/my-roles-02.png)
