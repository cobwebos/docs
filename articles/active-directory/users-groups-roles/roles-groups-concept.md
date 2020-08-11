---
title: 使用云组来管理 Azure Active Directory 中的角色分配 |Microsoft Docs
description: 预览用于委托标识管理的自定义 Azure AD 角色。 在 Azure 门户、PowerShell 或图形 API 中管理 Azure 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65aed14ec7f644f2792aaecde5c8bccfffdd8081
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078438"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>使用云组来管理 Azure Active Directory (预览版中的角色分配) 

Azure Active Directory (Azure AD) 引入公共预览，你可以将云组分配到 Azure AD 内置角色。 利用此功能，你可以使用组授予对 Azure AD 的管理员访问权限，从而最大程度地降低全局角色和特权管理员。

请考虑以下示例： Contoso 已在多个地理区域雇用人员，以便为其 Azure AD 组织中的员工管理和重置密码。 他们可以创建一个 Contoso_Helpdesk_Administrators 组，并将其分配给角色，而不是让特权角色管理员或全局管理员分别向每个人分配支持人员管理员角色。 当用户加入组时，将间接为他们分配角色。 然后，你的现有调控工作流可以处理批准过程并审核组的成员身份，以确保只有合法用户成为组的成员，并将其分配给支持人员管理员角色。

## <a name="how-this-feature-works"></a>此功能的工作原理

创建新的 Office 365 或 "isAssignableToRole" 属性设置为 "true" 的安全组。 在 Azure 门户中创建组时，还可以启用此属性，方法是打开**可分配给组 Azure AD 角色**。 无论采用哪种方式，你都可以将组分配给一个或多个 Azure AD 角色，方法与向用户分配角色时相同。 可在单个 Azure AD 组织 (租户) 中创建最多可分配200个角色的组。

如果你不希望组的成员拥有对角色的访问权限，则可以使用 Azure AD Privileged Identity Management。 分配组作为 Azure AD 角色的合格成员。 然后，组的每个成员都有资格为该组分配到的角色激活分配。 然后，他们可以激活固定时间段内的角色分配。

> [!Note]
> 必须处于更新版本的 Privileged Identity Management，才能通过 PIM 将组分配给 Azure AD 角色。 你可以在较旧版本的 PIM 上，因为你的 Azure AD 组织利用 Privileged Identity Management API。 请联系别名， pim_preview@microsoft.com 以移动组织并更新 API。 有关详细信息[，请参阅 AZURE AD PIM 中的角色和功能](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-roles-features)。

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>为什么我们强制创建特殊组来将其分配给角色

如果为组分配了角色，则任何可以管理组成员身份的 IT 管理员也可以间接管理该角色的成员身份。 例如，假设组 Contoso_User_Administrators 分配给用户帐户管理员角色。 可以修改组成员身份的 Exchange 管理员可以将自己添加到 Contoso_User_Administrators 组中，这样就成为了用户帐户管理员。正如您所看到的，管理员可以使用您不打算的方式提升其权限。

Azure AD 允许使用名为 isAssignableToRole 的新属性来保护分配给角色的组。 只能在创建时将 isAssignableToRole 属性设置为 "true" 的云组分配给角色。 此属性是不可变的;使用此属性设置为 "true" 创建组后，将无法对其进行更改。 不能对现有组设置属性。
我们设计了如何将组分配给角色，以防止发生潜在的破坏：

- 只有全局管理员和特权角色管理员才能创建可分配角色的组， () 启用了 "isAssignableToRole" 属性。
- 它不能是 Azure AD 动态组;也就是说，它必须具有成员资格类型 "已分配"。 自动填充动态组可能会导致向组添加不需要的帐户，从而将其分配给该角色。
- 默认情况下，只有全局管理员和特权角色管理员可以管理角色可分配的组的成员身份，但你可以通过添加组所有者来委派角色可分配的组的管理。
- 若要防止特权提升，可分配角色的组的成员和所有者的凭据只能由特权身份验证管理员或全局管理员进行更改。
- 无嵌套。 不能将组添加为角色可分配的组的成员。

## <a name="limitations"></a>限制

当前不支持以下方案：  

- 将云组分配到 Azure AD 自定义角色
- 将云组分配给管理单元或应用程序范围 (内置或自定义) Azure AD 角色。
- 将本地组分配给 (内置或自定义) Azure AD 角色

## <a name="known-issues"></a>已知问题

- 如果角色是通过组分配的，则不能创建或修改动态组。
- **为托管用户登录功能启用暂存推出**不支持通过组进行分配。
- *仅 Azure AD P2 许可客户*：不要通过 Azure AD 和 Privileged Identity Management 为角色分配活动组。 这将导致用户在 PIM 中看不到其活动角色分配以及无法删除 PIM 赋值的问题。 符合条件的分配在此方案中不受影响。 如果尝试进行此分配，可能会出现意外的行为，例如：
  - 角色分配的结束时间可能会错误地显示。
  - 在 PIM 门户中， **"我的角色**" 只能显示一个角色分配，而与通过一个或多个组 (授予分配的方法多少并直接) 。
- *仅 Azure AD P2 授权客户*即使在删除组之后，它仍会在 PIM UI 中显示为该角色的合格成员。 功能没有问题;这只是 Azure 门户中的缓存问题。  
- Exchange 管理中心不会通过组识别角色成员身份，但 PowerShell cmdlet 将起作用。
- Azure 信息保护门户 (经典门户) 不通过组识别角色成员身份。 你可以[迁移到统一的灵敏度标签平台](https://docs.microsoft.com/azure/information-protection/configure-policy-migrate-labels)，然后使用 Office 365 Security & 相容性中心来使用组分配来管理角色。

我们正在解决这些问题。

## <a name="required-license-plan"></a>所需许可证计划

使用此功能要求你在 Azure AD 组织中具有可用的 Azure AD Premium P1 许可证。 若要同时使用 Privileged Identity Management 以进行实时角色激活，需要具有可用的 Azure AD Premium P2 许可证。 若要根据需要查找正确的许可证，请参阅[比较免费和高级计划的一般可用功能](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)。

## <a name="next-steps"></a>后续步骤

- [创建可分配角色的组](roles-groups-create-eligible.md)
- [将角色分配给角色可分配的组](roles-groups-assign-role.md)
