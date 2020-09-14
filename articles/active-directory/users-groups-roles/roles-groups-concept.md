---
title: 使用云组来管理 Azure Active Directory 中的角色分配 | Microsoft Docs
description: 预览用于委托标识管理的自定义 Azure AD 角色。 管理 Azure 门户、PowerShell 或图形 API 中的 Azure 角色分配。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/11/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2932bfc2f9606326ae38711237e5e10912d41aca
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053763"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>使用云组来管理 Azure Active Directory（预览版）中的角色分配

Azure Active Directory (Azure AD) 即将引入公共预览，可在其中向 Azure AD 内置角色分配云组。 使用此功能，可以使用组轻松地在 Azure AD 中通过“全局”和“特权”角色管理员来授予管理员访问权限。

请看以下示例：Contoso 已在多个地理区域雇用人员，以便为其 Azure AD 组织中的员工管理和重置密码。 他们可以创建 Contoso_Helpdesk_Administrators 组并将其分配给角色，而不是要求特权角色管理员或全局管理员分别为每个人分配支持管理员角色。 当用户加入组时，将间接为他们分配角色。 然后，你现有的治理工作流可以处理审批过程和审核组成员身份，以确保只有合法用户才能成为该组成员，并因此将其分配给支持管理员角色。

## <a name="how-this-feature-works"></a>该功能的工作原理

创建新的 Microsoft 365 或安全组，将 "isAssignableToRole" 属性设置为 "true"。 还可以通过启用“Azure AD 角色可以分配到组”，在 Azure 门户中创建组时启用此属性。 无论采用哪种方式，你都可以将组分配给一个或多个 Azure AD 角色，方法与为用户分配角色的方式相同。 在单个 Azure AD 组织（租户）中最多可以创建 200 个可分配角色的组。

如果不希望组成员具有对角色的现有访问权限，则可以使用 Azure AD Privileged Identity Management。 将组分配为 Azure AD 角色的符合条件的成员。 然后，该组中的每个成员都有资格为分配给该组的角色激活其分配。 然后，他们可以在固定的时间内激活角色分配。

> [!Note]
> 必须使用 Privileged Identity Management 的更新版本才能通过 PIM 将组分配给 Azure AD 角色。 你可以使用旧版本的 PIM，因为你的 Azure AD 组织使用的是 Privileged Identity Management API。 请联系别名 pim_preview@microsoft.com 来移动你的组织并更新你的 API。 有关详细信息，请参阅 [PIM 中的 Azure AD 角色和功能](../privileged-identity-management/azure-ad-roles-features.md)。

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>我们为何要强制创建特殊组来将其分配给角色

如果为组分配了角色，则任何可以管理组成员身份的 IT 管理员也可以间接管理该角色的成员身份。 例如，假定将组 Contoso_User_Administrators 分配给用户帐户管理员角色。 可以修改组成员身份的 Exchange 管理员可以将自己添加到 Contoso_User_Administrators 组中，从而成为用户帐户管理员。如你所见，管理员可以以你不希望的方式提升他们的特权。

通过 Azure AD 可使用名为 isAssignableToRole 的新属性来保护分配给某个角色的组。 只有在创建时将 isAssignableToRole 属性设置为“true”的云组才能分配给角色。 此属性是不可变的；一旦创建了一个将此属性设置为“true”的组，则无法更改它。 不能对现有组设置属性。
我们设计了如何将组分配给角色，以防止出现这种潜在的漏洞：

- 只有全局管理员和特权角色管理员才能创建可分配角色的组（启用“isAssignableToRole”属性）。
- 它不能是 Azure AD 动态组;也就是说，它必须具有成员资格类型 "已分配"。 自动填充动态组可能会导致向组添加不需要的帐户，从而将其分配给该角色。
- 默认情况下，只有全局管理员和特权角色管理员可以管理可分配角色组的成员资格，但你可以通过添加组所有者来委派对可分配角色的组的管理。
- 为了防止特权提升，只能由特权身份验证管理员或全局管理员更改可分配角色组的成员和所有者的凭据。
- 无嵌套。 不能将组添加为角色可分配的组的成员。

## <a name="limitations"></a>限制

当前不支持以下方案：  

- 将云组分配到 Azure AD 自定义角色
- 将云组分配给管理单元或应用程序范围内的 Azure AD 角色（内置或自定义）。
- 将本地组分配给 Azure AD 角色（内置或自定义）

## <a name="known-issues"></a>已知问题

- 如果角色是通过组分配的，则不能创建或修改动态组。
- “为托管用户登录名启用分阶段推出”功能不支持通过组分配。
- *仅限 Azure AD P2 授权客户*：不要通过 Azure AD 和 Privileged Identity Management (PIM) 为角色分配活动组。 具体而言，在创建一个可分配角色的组时，不要将角色分配给该组，也不要在以后使用 PIM 时将角色分配给该组。 这将导致用户无法在 PIM 中看到其活动角色分配以及无法删除该 PIM 分配的问题。 符合条件的分配在此方案中不受影响。 如果尝试进行此分配，可能会出现意外的行为，例如：
  - 角色分配的结束时间可能显示错误。
  - 在 PIM 门户中，“我的角色”只能显示一个角色分配，而不管通过多少方法授予分配（通过一个或多个组直接进行）。
- 仅限 Azure AD P2 授权客户。即使删除该组，它仍会在 PIM UI 中显示该角色的合格成员。 在功能上没有问题；这只是 Azure 门户中的缓存问题。  
- Exchange 管理中心尚无法通过组识别角色成员身份，但 PowerShell cmdlet 将起作用。
- Azure 信息保护门户 (经典门户) 不通过组识别角色成员身份。 你可以 [迁移到统一的灵敏度标签平台](/azure/information-protection/configure-policy-migrate-labels) ，然后使用 Office 365 Security & 相容性中心来使用组分配来管理角色。

我们正在解决这些问题。

## <a name="required-license-plan"></a>所需许可证计划

使用此功能要求你在 Azure AD 组织中具有可用的 Azure AD Premium P1 许可证。 若要同时使用 Privileged Identity Management 进行即时角色激活，需要具有可用的 Azure AD Premium P2 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费和高级计划的正式发布功能](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)。

## <a name="next-steps"></a>后续步骤

- [创建可分配角色的组](roles-groups-create-eligible.md)
- [将角色分配给可分配角色的组](roles-groups-assign-role.md)