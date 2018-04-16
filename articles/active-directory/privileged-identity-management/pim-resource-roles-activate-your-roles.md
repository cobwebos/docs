---
title: Azure 资源的 Privileged Identity Management - 激活角色 | Microsoft Docs
description: 介绍如何在 PIM 中激活角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - 资源角色 - 激活
Azure 资源的激活角色功能引入了新的体验，可让有资格的角色成员计划在将来的某个日期/时间进行激活，并在最大时间范围（由管理员配置）内选择特定的激活持续时间。 在此处了解[如何激活 Azure AD 角色](../active-directory-privileged-identity-management-how-to-activate-role.md)。

## <a name="activate-roles"></a>激活角色
导航到左侧导航栏上的“我的角色”部分。 对想要激活的角色单击“激活”。
![](media/azure-pim-resource-rbac/rbac-roles.png)

通过“激活”菜单输入所需的开始日期和时间以激活角色。 （可选）减小激活持续时间（角色保持活动状态的时间长短），根据需要输入理由；单击“激活”。

如果未修改开始日期和时间，角色在几秒内即会激活。 “我的角色”页面上会出现一条横幅消息，指出某个角色已排队等待激活。 单击刷新按钮可清除此消息。

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

如果计划在将来的某个日期时间激活，挂起的请求会显示在左侧导航菜单的“挂起的请求”选项卡中。 如果不再需要激活角色，用户可以通过单击页面右侧的“取消”按钮取消请求。

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Just Enough Administration

在适用于 Azure 资源的 PIM 中，可以轻松针对资源角色分配使用 Just Enough Administration (JEA) 最佳做法。 Azure 订阅或资源组中获得分配的用户和组成员可以在更小的范围内激活其现有角色分配。 

在搜索页面中，找到需要管理的附属资源。

![](media/azure-pim-resource-rbac/azure-resources-02.png)

在左侧导航菜单中选择“我的角色”，并选择要激活的相应角色。 请注意分配类型为“继承”，因为角色是在订阅级别而不是资源组级别分配的，如下所示。

![](media/azure-pim-resource-rbac/my-roles-02.png)
