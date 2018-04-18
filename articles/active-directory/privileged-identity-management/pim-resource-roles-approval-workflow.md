---
title: Privileged Identity Management - Azure 资源角色的审批工作流 | Microsoft Docs
description: 介绍 Azure 资源的审批工作流程。
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
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - 资源角色 - 审批

借助 PIM 中 Azure 资源角色的审批工作流，管理员可以要求审批激活角色分配，来进一步保护或限制对关键资源的访问。 资源层次结构的概念是 Azure 资源角色的独特之处。 使用此层次结构，可将父资源对象中的角色分配向下继承到父容器中的所有从属子资源。 

例如，Bob（资源管理员）使用 PIM 将 Alice 作为合格成员分配到 Contoso 订阅中的“所有者”角色。 通过这种分配，Alice 还是 Contoso 订阅中所有资源组容器以及该订阅的每个资源组中包含的所有资源（例如虚拟机）的合格所有者。 假设 Contoso 订阅中有三个资源组：Fabrikam Test、Fabrikam Dev 和 Fabrikam Prod。其中每个资源组包含单个虚拟机。

PIM 设置是针对资源的每个角色配置的。与分配不同，这些设置不是继承的，且会严格应用到资源角色。 [详细了解合格分配和资源可见性。](pim-resource-roles-eligible-visibility.md)

沿用上面的示例，Bob 使用 PIM 来要求 Contoso 订阅“所有者”角色的所有成员请求激活审批。 为了保护 Fabrikam Prod 资源组中包含的资源，Bob 还要求此资源的“所有者”角色成员请求审批。 Fabrikam Test 和 Fabrikam Dev 中的所有者角色不需要请求激活审批。

当 Alice 请求激活 Contoso 订阅的“所有者”角色时，审批者必须在 Alice 激活角色之前批准或拒绝她的请求。 此外，如果 Alice 决定[将激活范围限定为](pim-resource-roles-activate-your-roles.md#just-enough-administration) Fabrikam Prod 资源组，则审批者也必须批准或拒绝此请求。 但是，如果 Alice 决定将激活范围限定为 Fabrikam Test 和/或 Fabrikam Dev，则不需要审批。

不一定要对所有角色成员执行审批工作流。 假设你的组织招聘了几个合同工来帮助开发要在 Azure 订阅中运行的应用程序。 作为资源管理员，你希望正式员工可以在不需要审批的情况下进行合格访问，但合同工必须请求审批。 如果只是为合同工配置审批工作流，可以使用分配给正式员工的角色所拥有的相同权限创建一个自定义角色，并要求审批该自定义角色的激活。 [详细了解自定义角色](pim-resource-roles-custom-role-policy.md)。

遵循以下步骤配置审批工作流，并指定谁可以批准或拒绝请求。

## <a name="require-approval-to-activate"></a>需要批准才能激活

在 Azure 门户中导航到 PIM，并从列表中选择一个资源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

在左侧导航菜单中选择“角色设置”。

搜索并选择一个角色，单击“编辑”以修改设置。

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

在“激活”部分，选中“需要批准才能激活”框。

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>指定审批者

单击“选择审批者”打开选择屏幕。

>[!NOTE]
>必须至少选择一个用户或组才能更新设置。 没有默认的审批者。

资源管理员可将用户和组的任意组合添加到审批者列表。 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>请求批准激活

请求批准不会影响到某个成员在激活时必须遵循的过程。 [审查激活角色的步骤](pim-resource-roles-activate-your-roles.md)。

如果某个成员请求激活一个需要审批的角色，但不再需要该角色，则该成员可以在 PIM 中取消其请求。

若要取消，请导航到 PIM 并选择“我的请求”。 找到该请求，单击“取消”。

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>批准或拒绝请求

只有审批者列表的成员才能批准或拒绝请求。 在 PIM 中，从左侧导航菜单中的选项卡选择“审批请求”，并找到该请求。

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

选择该请求，提供做出该决定的理由，选择批准或拒绝，然后解决请求。

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>工作流通知

- 当角色请求正在等待其审查时，审批者列表的所有成员会收到电子邮件通知。 电子邮件通知包含请求的直接链接，审批者可通过此链接批准或拒绝请求。
- 请求将由列表中第一个做出批准或拒绝决定的成员来解决。 
- 当审批者响应请求时，审批者列表的所有成员都会收到操作通知。 
- 获批准的成员激活其角色后，资源管理员会收到通知。 

>[!Note]
>如果资源管理员认为获批准的成员不应被激活，他们可以在 PIM 中删除已激活的角色分配。 尽管资源管理员不会收到等待处理请求的通知（除非他们是审批者列表的成员），但他们可以通过在 PIM 中查看等待处理的请求，来查看和取消所有用户的等待处理请求。 

## <a name="next-steps"></a>后续步骤

[将 PIM 设置应用到用户的独特组](pim-resource-roles-custom-role-policy.md)
