---
title: Privileged Identity Management 中 Azure 资源角色的审批工作流 | Microsoft Docs
description: 介绍 Azure 资源的审批工作流程。
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
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dcb306bda8ef7d93314390bd9a90327ffdd14a0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619631"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management 中 Azure 资源角色的审批工作流

借助 Privileged Identity Management (PIM) 中 Azure 资源角色的审批工作流，管理员可进一步保护或限制对关键资源的访问。 也就是说，管理员可以要求批准以激活角色分配。 

资源层次结构的概念是 Azure 资源角色的独特之处。 使用此层次结构，可将父资源对象中的角色分配向下继承到父容器中的所有子资源。 

例如，Bob（资源管理员）使用 PIM 将 Alice 作为合格成员分配到 Contoso 订阅中的“所有者”角色。 分配后，Alice 即成为该 Contoso 订阅中所有资源组容器的合格所有者。 Alice 也是该订阅每个资源组内所有资源（如虚拟机）的合格所有者。 

假设 Contoso 订阅中有三个资源组：Fabrikam Test、Fabrikam Dev 和 Fabrikam Prod。其中每个资源组包含单个虚拟机。

PIM 设置是针对资源的每个角色配置的。 与分配不同，这些设置不是继承的，且会严格应用到资源角色。 [详细了解合格分配和资源可见性](pim-resource-roles-eligible-visibility.md)。

继续沿用该示例：Bob 使用 PIM 来要求 Contoso 订阅“所有者”角色中的所有成员请求激活审批。 为帮助保护 Fabrikam Prod 资源组中的资源，Bob 还要求审批此资源的“所有者”角色的成员。 Fabrikam Test 和 Fabrikam Dev 中的“所有者”角色不需要请求激活审批。

当 Alice 请求激活 Contoso 订阅的“所有者”角色时，审批者必须在 Alice 激活角色之前批准或拒绝她的请求。 如果 Alice 决定[将激活范围限定为](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) Fabrikam Prod 资源组，则审批者也必须批准或拒绝此请求。 但如果 Alice 决定将激活范围限定为 Fabrikam Test 和/或 Fabrikam Dev，则不需要审批。

不一定要对所有角色成员执行审批工作流。 假设你的组织招聘了几名合同工来帮助开发将在 Azure 订阅中运行的应用程序。 作为资源管理员，你希望正式员工可以在不需要审批的情况下获得合格访问权，但合同工必须请求审批。 如果只是为合同工配置审批工作流，则可创建一个自定义角色，该角色的权限与分配给正式员工的角色所拥有的权限相同。 你可以要求批准以激活该自定义角色。 [详细了解自定义角色](pim-resource-roles-custom-role-policy.md)。

若要配置审批工作流并指定谁可以批准或拒绝请求，请按以下步骤操作。

## <a name="require-approval-to-activate"></a>需要批准才能激活

1. 在 Azure 门户中浏览到 PIM，并从列表中选择一个资源。

   ![“Azure 资源”窗格，其中选择了一个资源](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. 在左侧窗格中，选择“角色设置”。

3. 搜索并选择一个角色，然后选择“编辑”以修改设置。

   ![操作者角色的“编辑”按钮](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. 在“激活”部分，选择“需要批准才能激活”复选框。

   ![角色设置的“激活”部分](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>指定审批者

单击“选择审批者”打开“选择用户或组”窗格。

>[!NOTE]
>必须至少选择一个用户或组才能更新设置。 没有默认的审批者。

资源管理员可将用户和组的任意组合添加到审批者列表。 

![“选择用户或组”窗格，其中选择了一个用户](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>请求批准激活

请求批准不会影响到成员在激活时必须遵循的过程。 [审查激活角色的步骤](pim-resource-roles-activate-your-roles.md)。

如果某个成员请求激活一个需要审批的角色，但不再需要该角色，则该成员可以在 PIM 中取消其请求。

若要取消，转到 PIM 并选择“我的请求”。 找到该请求，选择“取消”。

![“我的请求”窗格](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>批准或拒绝请求

只有审批者列表的成员才能批准或拒绝请求。 

1. 在 PIM 中，选择左侧菜单上的“审批请求”选项卡，并找到该请求。

   ![“审批请求”窗格](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. 选择该请求，提供做出该决定的理由，选择“批准”或“拒绝”。 请求随即被取消。

   ![所选请求及其详细信息](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>工作流通知

下面是一些有关工作流通知的事项：

- 当角色请求正在等待其审查时，审批者列表的所有成员会收到电子邮件通知。 电子邮件通知包含请求的直接链接，审批者可通过此链接批准或拒绝请求。
- 请求将由列表中第一个做出批准或拒绝决定的成员来解决。 
- 当审批者响应请求时，审批者列表的所有成员都会收到操作通知。 
- 获批准的成员激活其角色后，资源管理员会收到通知。 

>[!Note]
>如果资源管理员认为获批准的成员不应被激活，则可在 PIM 中删除已激活的角色分配。 尽管资源管理员不会收到等待处理请求的通知（除非他们是审批者列表的成员），但他们可通过在 PIM 中查看等待处理的请求，来查看和取消所有用户等待处理的请求。 

## <a name="next-steps"></a>后续步骤

[将 PIM 设置应用到用户的独特组](pim-resource-roles-custom-role-policy.md)
