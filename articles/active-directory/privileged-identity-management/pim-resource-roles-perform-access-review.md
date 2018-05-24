---
title: 在 Privileged Identity Management 中对 Azure 资源执行访问评审 | Microsoft Docs
description: 本文档介绍如何根据资源角色在 PIM 中对 Azure 资源执行访问评审。
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
ms.openlocfilehash: 47d981ce8034d725fe2b119392334a156852698d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201583"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>根据资源角色，在 PIM 中执行访问评审
Azure 资源的 Privileged Identity Management (PIM) 简化了企业对 Azure 中的资源的特权访问管理。 

如果已被分配到某个管理角色，组织中的特权角色管理员可能要求定期确认仍然需要使用该角色来完成工作。 可以通过你所收到的电子邮件中的链接，或者直接转到 [Azure 门户](https://portal.azure.com)来执行此操作。 请遵循本文中的步骤对分配的角色执行自我审查。

如果是特权角色管理员并且想要了解访问权限审查，请参阅[如何开始进行访问权限审查](pim-resource-roles-start-access-review.md)中的详细信息。

## <a name="add-the-privileged-identity-management-application"></a>添加 Privileged Identity Management 应用程序
可以使用 [Azure 门户](https://portal.azure.com/)中的 Azure Active Directory (Azure AD) PIM 应用程序执行审查。 如果你的门户中没有该应用程序，请按照以下步骤开始。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，并选择要操作的目录。
3. 选择“所有服务”，并使用“筛选器”框搜索“Azure AD Privileged Identity Management”。
4. 选择“固定到仪表板”，然后选择“创建”。 PIM 应用程序随即打开。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限
批准或拒绝访问权限时，只是告诉审查人你是否仍要使用此角色。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。 你的状态仅在审阅者应用结果时才会更改。

请遵循以下步骤来查找并完成访问权限审查：
1. 浏览到 Azure AD PIM 应用程序。
2. 选择“审阅访问权限”边栏选项卡。

   ![PIM 应用程序屏幕截图，选中了“审阅访问权限”边栏选项卡](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. 选择想要完成的审查。 
4. 选择“批准”或“拒绝”。 可能需要在“提供原因”文本框中提供做出该决定的原因。

   ![“审阅详细信息”页的屏幕截图](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
