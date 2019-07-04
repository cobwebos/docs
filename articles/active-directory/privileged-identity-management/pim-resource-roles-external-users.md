---
title: 邀请来宾并将 PIM 的 Azure Active Directory 中的 Azure 资源角色分配 |Microsoft Docs
description: 了解如何邀请外部来宾用户并将在 Azure AD Privileged Identity Management (PIM) 中的 Azure 资源角色分配。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07476c9f5db64a5d107a493022fa3548fe0dae4c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476342"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>邀请来宾用户并分配在 PIM 中的 Azure 资源角色

Azure Active Directory (Azure AD) 企业到企业 (B2B) 是使组织进行协作与外部来宾用户 （来宾） 和供应商使用任何帐户的 Azure AD 中的一组功能。 将 B2B 与 Azure AD Privileged Identity Management (PIM) 时，可以继续应用于来宾法规遵从性和监管要求。 例如，您可以使用这些 PIM 功能的 Azure 标识任务与来宾：

- 分配对特定 Azure 资源的访问权限
- 启用实时访问
- 指定分配持续时间和结束日期
- 要求对活动分配或激活进行 MFA
- 执行访问评审
- 使用警报和审核日志

本文介绍如何邀请来宾到你的组织和管理他们对使用 Privileged Identity Management 的 Azure 资源的访问。

## <a name="when-would-you-invite-guests"></a>何时您将邀请来宾？

下面是几个示例方案时您可能会邀请来宾到你的组织：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许使用本地 Active Directory 联合身份验证服务的大型组织的外部合作伙伴访问费用应用程序。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>如何执行协作使用 B2B 来宾工作？

使用 B2B 协作时，可以对组织作为来宾邀请外部用户。 来宾似乎是在你的组织，但来宾没有与之关联的任何凭据。 每当来宾进行身份验证时，他们必须进行身份验证在其本组织并不在你的组织。 这意味着，如果来宾不再有权访问其本组织，他们也无法访问你的组织。 例如，如果来宾离开组织，它们会自动失去访问权限你与他们共享在 Azure AD 中而无需执行任何操作的任何资源。 有关 B2B 的详细信息，请参阅[什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../b2b/what-is-b2b.md)。

![关系图的显示如何来宾用户在目录中，将出现，但在其主目录中进行身份验证](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>检查来宾协作设置

若要确保你可以邀请来宾到你的组织，应检查来宾协作设置。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 单击“Azure Active Directory” > “用户设置”。  

1. 单击“管理外部协作设置”。 

    ![外部协作设置页上显示的权限、 邀请和协作限制设置](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 确保“管理员和具有来宾邀请者角色的用户可以邀请”开关设置为“是”。  

## <a name="invite-a-guest-and-assign-a-role"></a>邀请来宾并分配角色

使用 PIM，可以邀请来宾，并使其符合 Azure 资源角色成员用户一样的资格。

1. 登录到[Azure 门户](https://portal.azure.com/)与用户的成员[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或[用户管理员](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)角色。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure 资源”  。

1. 使用资源筛选器  对托管资源的列表进行筛选。

1. 单击要管理的资源，如资源、资源组、订阅或管理组。

    您应该只需要哪些来宾操作系统设置的作用域。

1. 在“管理”下，单击“角色”  以查看 Azure 资源的角色列表。

    ![Azure 资源角色列表显示处于活动状态且符合条件的用户数](./media/pim-resource-roles-external-users/resources-roles.png)

1. 单击用户所需的最小角色。

    ![选择列出该角色的当前成员的角色页](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色页面，单击“添加成员”  以打开“新建分配”窗格。

1. 单击“选择成员或组”。 

    ![新分配-选择一个成员或组窗格列出用户和组以及邀请选项](./media/pim-resource-roles-external-users/select-member-group.png)

1. 若要邀请来宾，请单击**邀请**。

    ![邀请来宾页包含文本框用于输入的电子邮件地址，并指定个人消息](./media/pim-resource-roles-external-users/invite-guest.png)

1. 选择来宾后，单击**邀请**。

    应将来宾添加为所选的成员。

1. 在中**选择成员或组**窗格中，单击**选择**。

1. 在中**成员身份设置**窗格中，选择分配类型和持续时间。

    ![新分配的成员身份设置页，并且可以选择指定分配类型、 开始日期和结束日期](./media/pim-resource-roles-external-users/membership-settings.png)

1. 单击“完成”，然后单击“添加”，以完成分配。  

    来宾角色分配会在你的角色列表中。

    ![列出来宾有资格的角色页](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>激活角色作为来宾

为外部用户，您首先需要接受邀请到你的 Azure AD 组织并可能激活你的角色。

1. 打开与你的邀请电子邮件。 该电子邮件如下所示。

    ![电子邮件邀请，其中包含目录名称、 个人消息和开始使用链接](./media/pim-resource-roles-external-users/email-invite.png)

1. 单击电子邮件中的“开始”链接。 

1. 查看权限后，单击“接受”。 

    ![在浏览器使用的组织想要查看的权限列表中的查看权限页](./media/pim-resource-roles-external-users/invite-accept.png)

1. 您可能需要接受了使用条款，并指定是否想要保持登录状态。

    此时会打开 Azure 门户。 若你刚刚才符合某个角色的条件，则没有对资源的访问权限。

1. 请打开包含激活角色链接的电子邮件，以激活角色。 该电子邮件如下所示。

    ![从 PIM，该值指示该你符合条件的角色激活角色链接的电子邮件](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 单击“激活角色”，以在 PIM 中打开你的符合条件的角色。 

    ![我在列出你符合条件的角色的 PIM 中的角色页面](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在“操作”下单击“激活”链接。 

    需要指定一些信息来激活角色，具体取决于角色设置。

1. 指定角色设置后，单击“激活”，以激活角色。 

    ![激活页列表作用域和要指定开始时间、 持续时间和原因的选项](./media/pim-resource-roles-external-users/activate-role.png)

    如果并未要求管理员批准你的请求，则你应具有了对指定资源的访问权限。

## <a name="view-activity-for-a-guest"></a>查看活动的来宾

就像成员用户可以查看审核日志来跟踪来宾正在执行哪些操作。

1. 以管理员身份打开 PIM，并选择已共享的资源。

1. 单击“资源审核”，以查看该资源的活动。  以下示例演示了资源组的活动。

    ![Azure 资源的资源审核页列出的时间、 请求者和操作](./media/pim-resource-roles-external-users/audit-resource.png)

1. 若要查看活动的来宾操作系统，请单击**Azure Active Directory** > **用户**> 来宾名称。

1. 单击**审核日志**以查看组织的审核日志。 可以根据需要指定筛选器。

    ![目录审核日志列表日期、 目标，由，启动和活动](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>后续步骤

- [分配 Azure AD PIM 中的管理员角色](pim-how-to-add-role-to-user.md)
- [什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../b2b/what-is-b2b.md)
