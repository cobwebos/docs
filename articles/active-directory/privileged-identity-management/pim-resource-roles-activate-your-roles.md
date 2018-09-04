---
title: 在 PIM 中激活 Azure 资源角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中激活 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189483"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中激活 Azure 资源角色
Privileged Identity Management (PIM) 引入了激活 Azure 资源角色的新体验。 符合条件的角色成员可计划在未来某个时间激活角色。 他们还可选择特定激活持续时间，但不能超过最长持续时间（由管理员配置）。 有关详细信息，请参阅[如何在 Azure AD Privileged Identity Management 中激活或停用角色](pim-how-to-activate-role.md)。

## <a name="activate-a-role"></a>激活角色
转到左侧窗格中“我的角色”部分。 针对要激活的角色，选择“激活”。

![“我的角色”窗格中“符合条件的角色”选项卡。](media/azure-pim-resource-rbac/rbac-roles.png)

在“激活”菜单中输入激活角色的开始日期和时间。 可根据需要缩短激活持续时间（角色保持活动状态的时长），并根据需要输入理由。 然后选择“激活”。

如果未修改开始日期和时间，角色在几秒内即会激活。 在“我的角色”窗格中，横幅消息显示某个角色已排队等待激活。 选择刷新按钮可清除此消息。

![显示横幅信息和待批准通知的“我的角色”窗格](media/azure-pim-resource-rbac/rbac-activate-notification.png)

如果计划在将来的某个时间进行激活，挂起的请求会显示在左侧窗格的“挂起的请求”选项卡中。 如果不在需要角色激活，则可选择“取消”按钮，取消该请求。

![带“取消”按钮的挂起的请求列表](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>在激活后立即使用角色

由于缓存，在未刷新的情况下，Azure 门户中不会立即进行激活。 如果需要在激活角色后减少延迟的可能性，则可以使用门户中的“应用程序访问”页。 从此页访问的应用程序会立即检查新的角色分配。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“应用程序访问”页。

    ![PIM 应用程序访问 - 屏幕截图](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. 单击“Azure 资源”以在“所有资源”页上重新打开门户。

    单击此链接时，将强制刷新，并检查是否有新的 Azure 资源角色分配。

## <a name="apply-just-enough-administration-practices"></a>应用 Just Enough Administration 做法

在适用于 Azure 资源的 PIM 中，可以轻松针对资源角色分配使用 Just Enough Administration (JEA) 最佳做法。 Azure 订阅或资源组中获得分配的用户和组成员可以在更小的范围内激活其现有角色分配。 

在搜索页面中，找到需要管理的附属资源。

![选择资源](media/azure-pim-resource-rbac/azure-resources-02.png)

在左侧窗格中选择“我的角色”，并选择要激活的相应角色。 分配类型为“继承”，因为角色是在订阅级别而不是资源组级别分配的。

![符合条件的角色分配列表，其中突出显示了分配类型](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中激活 Azure AD 目录角色](pim-how-to-activate-role.md)