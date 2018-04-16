---
title: 如何在 Privileged Identity Management 中对 Azure 资源执行访问评审 | Microsoft Docs
description: 本文档介绍如何在 PIM 中对 Azure 资源执行访问评审。
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
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Privileged Identity Management - 资源角色 - 执行访问评审
Azure 资源的 Privileged Identity Management 简化了企业对 Azure 中的资源的特权访问管理。 

如果已被分配到某个管理角色，组织中的特权角色管理员可能要求定期确认仍然需要使用该角色来完成工作。 可以通过你所收到的电子邮件中的链接，或者直接转到 [Azure 门户](https://portal.azure.com)来执行此操作。 请遵循本文中的步骤对分配的角色执行自我审查。

如果是特权角色管理员并且想要了解访问权限审查，请参阅[如何开始进行访问权限审查](pim-resource-roles-start-access-review.md)中的详细信息。

## <a name="add-the-privileged-identity-management-application"></a>添加 Privileged Identity Management 应用程序
可以使用 [Azure 门户](https://portal.azure.com/)中的 Azure AD Privileged Identity Management (PIM) 应用程序执行审查。  如果门户中没有 Azure AD Privileged Identity Management 应用程序，请遵循以下步骤开始操作。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，并选择要操作的目录。
3. 选择“所有服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序会打开。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限
批准或拒绝访问权限时，只是告诉审查人你是否仍要使用此角色。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。 状态不会立即更改，只会在审查人应用结果之后才更改。
请遵循以下步骤来查找并完成访问权限审查：
1. 导航到 Azure AD Privileged Identity Management 应用程序。
2. 选择“评审访问权限”边栏选项卡

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. 选择要完成的评审。 
4. 选择“批准”或“拒绝”。 可能需要在“提供原因”文本框中提供做出该决定的原因。

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
