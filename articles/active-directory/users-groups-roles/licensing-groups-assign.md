---
title: 将许可证分配到组 - Azure Active Directory | Microsoft Docs
description: 如何通过 Azure Active Directory 组许可分配许可证
services: active-directory
keywords: Azure AD 许可
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c61bbc794438c34a4bda27c8048ac0b21f9fc1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582709"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>按 Azure Active Directory 中的组成员资格将许可证分配给用户

本文将指导你完成将产品许可证分配给一组用户，并验证他们是否在 Azure Active Directory (Azure AD) 中获得正确许可的过程。

在此示例中，Azure AD 组织包含名为**HR 部门**的安全组。 此组包括人力资源部的所有成员（大约 1,000 个用户）。 你要将 Office 365 企业版 E3 许可证分配给整个部门。 必须暂时禁用产品中包含的 Yammer Enterprise 服务，直到该部门准备好开始使用它为止。 你还希望将企业移动性 + 安全性许可证部署到同一个用户组。

> [!NOTE]
> 某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。
>
> 对于组许可证分配，任何没有指定使用位置的用户将继承该目录的位置。 如果你的用户位于多个位置，则建议你始终在 Azure AD 中的用户创建流程中设置使用位置（例如通过 AAD Connect 配置）- 这可确保许可证分配的结果始终正确并且用户不会在不允许的位置收到服务。

## <a name="step-1-assign-the-required-licenses"></a>步骤 1：分配所需的许可证

1. 使用许可证管理员帐户登录到[**Azure AD 管理中心**](https://aad.portal.azure.com)。 若要管理许可证，该帐户必须是许可证管理员、用户管理员或全局管理员。

1. 选择 "**许可证**" 以打开一个页面，您可以在其中查看和管理组织中的所有许可产品。

1. 在“所有产品”下，通过选择产品名称选中 Office 365 企业版 E5 和企业移动性 + 安全性 E3。  若要开始分配，请选择页面顶部的“分配”  。

   ![选择要分配许可证的产品](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. 在“分配许可证”页上，选择“用户和组”以打开用户和组的列表。  

1. 选择一个用户或组，然后使用页面底部的“选择”  按钮确认所做的选择。

1. 在“分配许可证”页上，单击“分配选项”，这将显示我们先前选择的两个产品中包括的所有服务计划。   查找“Yammer Enterprise”  并将设置为“关闭”  ，以便在产品许可证中禁用该服务。 在“许可证选项”的底部单击“确定”确认操作。  

   ![选择许可证的服务计划](./media/licensing-groups-assign/assignment-options.png)
  
1. 若要完成分配，请在“分配许可证”  页上，单击页面底部的“分配”  。

1. 右上角会显示一条通知，显示分配过程的状态和结果。 如果无法完成向组分配许可证（例如，由于该组中已有许可证），请单击该通知查看失败详细信息。

向组分配许可证时，Azure AD 将处理该组的所有现有成员。 此过程可能需要一些时间，因组的大小而异。 下一步骤介绍了如何验证该过程是否完成，并确定是否需要采取进一步的措施来解决问题。

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>步骤 2：验证初始分配是否已完成

1. 转到“Azure Active Directory”   > “组”  。 选择已向其分配许可证的组。

1. 在该组页上，选择“许可证”  。 这使你可以快速确认是否已完全将许可证分配给用户，以及是否有任何需要调查的错误。 该边栏选项卡中提供以下信息：

   - 当前分配到该组的服务许可证。 选择一个条目可以显示已启用的特定服务以及进行更改。

   - 最新许可证更改的状态更新，如果正在处理更改或所有用户成员的处理已经完成，则会显示这些状态更新。

   - 有关处于错误状态的用户许可证分配的信息。

   ![许可错误和许可证状态](./media/licensing-groups-assign/assignment-errors.png)

1. 可在“Azure Active Directory”   > “用户和组”   > “组名称”   > “审核日志”  下查看有关许可证处理的更多详细信息。 检查以下活动：

   - 活动：`Start applying group based license to users`。 当系统选取针对组所做的许可证分配更改并开始向所有用户成员应用该更改时，记录此活动。 其中包含有关所做的更改的信息。

   - 活动：`Finish applying group based license to users`。 当系统完成对组中所有用户的处理时，记录此活动。 它包含有关已成功处理多少个用户，以及无法为多少个用户分配组许可证的摘要。

   [阅读此部分](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity)以详细了解如何使用审核日志分析由基于组的许可进行的更改。

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>步骤 3：检查和解决许可证问题

1. 中转到**Azure Active Directory** > **组**，并找到已分配许可证的组。
1. 在该组页上，选择“许可证”****。 页面顶部的通知显示无法为 10 个用户分配许可证。 打开此通知可以看到该组中处于许可错误状态的所有用户的列表。
1. “失败的分配”列告知无法向这些用户分配这两个产品许可证。**** “故障的首要原因”**** 列包含失败的原因。 在此示例中，原因是**冲突的服务计划**。

   ![无法分配的许可证](./media/licensing-groups-assign/failed-assignments.png)

1. 选择用户以打开该用户的“许可证”**** 页。 此页显示当前已分配给该用户的所有许可证。 在此示例中，该用户具有从“网亭用户****”组继承的 Office 365 企业版 E1 许可证。 这与系统尝试从 **HR Department** 组应用的 E3 许可证冲突。 因此，该组中的许可证均未分配给该用户。

   ![查看用户的所有许可证冲突](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. 若要解决此问题，请从“网亭用户”**** 组中删除了该用户。 Azure AD 处理更改后，将正常分配“人力资源部”许可证。****

## <a name="next-steps"></a>后续步骤

若要详细了解使用组分配许可证的功能集，请参阅以下文章：

- [Azure Active Directory 中基于组的许可是什么？](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [识别和解决 Azure Active Directory 中组的许可问题](licensing-groups-resolve-problems.md)
- [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](licensing-groups-migrate-users.md)
- [如何在 Azure Active Directory 中使用基于组的许可在产品许可证之间迁移用户](licensing-groups-change-licenses.md)
- [基于 Azure Active Directory 组的许可的其他方案](../active-directory-licensing-group-advanced.md)
- [Azure Active Directory 中基于组的许可的 PowerShell 示例](licensing-ps-examples.md)
