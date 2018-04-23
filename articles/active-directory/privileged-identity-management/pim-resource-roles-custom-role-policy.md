---
title: Azure 资源的 Privileged Identity Management - 使用自定义角色来决定 PIM 设置针对的目标 | Microsoft Docs
description: 介绍了如何在 PIM 中为 Azure 资源使用自定义角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>使用自定义角色来决定 PIM 设置针对的目标

可能有必要向某个角色的某些成员应用严格的 PIM 设置，同时为其他人提供更大的自主权。 假设你的组织招聘了几个合同工来帮助开发将在 Azure 订阅中运行的应用程序。 

作为资源管理员，你希望你的组织的正式员工可以在不需审批的情况下进行合格访问，但是所有合同工在请求激活时必须请求审批。 按照下面列出了流程大纲的步骤来为 Azure 资源角色启用具有应用目标的 PIM 设置。

## <a name="create-the-custom-role"></a>创建自定义角色

[使用本指南针对资源创建自定义角色](../../role-based-access-control/custom-roles.md)。

请提供一个描述性名称，以便可以轻松记住你打算复制哪个内置角色。

>[!NOTE]
>请确保自定义角色是你所需角色的副本，并且其作用域与该内置角色匹配。

## <a name="apply-pim-settings"></a>应用 PIM 设置

在租户中创建角色后，导航到 PIM 并选择作为此角色的作用域的资源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[配置应当应用于这些成员的 PIM 角色设置](pim-resource-roles-configure-role-settings.md)

最后，为你希望作为这些设置的应用目标的不同成员组[分配角色](pim-resource-roles-assign-roles.md)。

## <a name="next-steps"></a>后续步骤

[查看订阅的所有者](pim-resource-roles-perform-access-review.md)
