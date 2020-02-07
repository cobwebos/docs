---
title: 在 PIM-Azure AD 中配置 Azure AD 角色设置 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中配置 Azure AD 角色设置。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a409d71ff3eae3bc62527a0669a74696246a50cd
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048087"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>在 Privileged Identity Management 中配置 Azure AD 角色设置

特权角色管理员可以在其 Azure Active Directory （Azure AD）组织中自定义 Privileged Identity Management （PIM），包括更改激活合格角色分配的用户的体验。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从2019年11月开始，Privileged Identity Management 的 Azure AD 角色部分更新为与 Azure 资源角色体验相匹配的新版本。 这将创建其他功能以及对[现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中所遵循的过程取决于当前所用 Privileged Identity Management 的版本。 按照本部分中的步骤确定 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本后，可以在本文中选择与该版本匹配的过程。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色的用户登录到[Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 如果在 "概述" 页顶部有一个横幅，请按照本文的 "**新版本**" 选项卡中的说明进行操作。 否则，请按照 "以前的**版本**" 选项卡中的说明进行操作。

    ![Azure AD 角色新版本](./media/pim-how-to-add-role-to-user/pim-new-version.png)

按照本文中的步骤批准或拒绝 Azure AD 角色的请求。

# <a name="new-versiontabnew"></a>[新版本](#tab/new)

## <a name="open-role-settings"></a>打开角色设置

遵循以下步骤打开 Azure AD 角色的设置。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色中的用户登录到[Azure 门户](https://portal.azure.com/)。
gt
1. 打开**Azure AD Privileged Identity Management** &gt; **Azure AD 角色**&gt;**角色设置**。

    ![列出 Azure 资源角色的角色设置页](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 选择要配置其设置的角色。

    ![列出多个分配和激活设置的角色设置详细信息页](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. 选择 "**编辑**" 以打开 "角色设置" 页。

    !["编辑角色设置" 页，其中包含用于更新分配和激活设置的选项](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    在每个角色的“角色设置”窗格上，有多个可以配置的设置。

## <a name="assignment-duration"></a>分配持续时间

配置角色的设置时，可以从用于每种分配类型（合格和活动）的两个分配持续时间选项中进行选择·。 当用户分配到 Privileged Identity Management 中的角色时，这些选项将成为默认的最大持续时间。

可以选择其中一个合格的分配持续时间选项：

| | |
| --- | --- |
| **允许永久的合格分配** | 资源管理员可以分配永久的合格分配。 |
| **使合格分配在以下时间后过期** | 资源管理员可以要求所有合格分配都具有指定的开始和结束日期。 |

并且，可以选择其中一个活动分配持续时间选项：

| | |
| --- | --- |
| **允许永久的活动分配** | 资源管理员可以分配永久活动分配。 |
| **使活动分配在以下时间后过期** | 资源管理员可以要求所有活动分配都具有指定的开始和结束日期。 |

> [!NOTE]
> 资源管理员可续订具有特定结束日期的所有分配。 此外，用户还可以启动自助服务请求来[扩展或续订角色分配](pim-resource-roles-renew-extend.md)。

## <a name="require-multi-factor-authentication"></a>需要多重身份验证

Privileged Identity Management 提供了两种不同的可选 Azure 多重身份验证强制执行方案。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>要求在活动分配时进行多重身份验证

在某些情况下，你可能想要将用户分配到一段较短的时间（例如一天）。 在这种情况下，分配的用户无需请求激活。 在这种情况下，当用户使用其角色分配时，Privileged Identity Management 无法强制实施多重身份验证，因为在分配该角色时，用户已在角色中处于活动状态。

若要确保满足分配的资源管理员是谁，可以通过选中 "**对活动分配需要多重身份验证**" 复选框，对活动分配强制执行多重身份验证。

### <a name="require-multi-factor-authentication-on-activation"></a>要求在激活时进行多重身份验证

你可以要求具有角色的用户能够在激活之前证明他们使用 Azure 多重身份验证的用户。 多重身份验证可确保用户是他们声称具有合理确定性的用户。 强制执行此选项可以在用户帐户可能已遭入侵的情况下保护关键资源。

若要在激活之前要求进行多重身份验证，请选中 "**激活时需要多重身份验证**" 复选框。

有关详细信息，请参阅[多重身份验证和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>最长激活持续时间

使用“最长激活持续时间”滑块是角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可以介于1到24小时之间。

## <a name="require-justification"></a>需要理由

您可以要求用户在激活时输入业务理由。 若需要理由，请选中“在活动分配时需要理由”框或“在激活时需要理由”框。

## <a name="require-approval-to-activate"></a>需要批准才能激活

如果要求批准以激活角色，请按照以下步骤操作。

1. 选中“需要批准以激活”复选框。

1. 选择 "**选择审批者**"。

    ![选择用户或组窗格以选择审批者](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 至少选择一个用户，然后单击 "**选择**"。 必须至少选择 1 个审批者。 没有默认的审批者。

    所选项将出现在所选审批者列表中。

1. 指定所有角色设置后，请选择 "**更新**" 以保存所做的更改。

# <a name="previous-versiontabprevious"></a>[先前版本](#tab/previous)

## <a name="open-role-settings"></a>打开角色设置

遵循以下步骤打开 Azure AD 角色的设置。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 单击“设置”。

    ![Azure AD 角色-设置](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. 选择“角色”。

1. 选择要配置其设置的角色。

    ![Azure AD 角色-设置角色](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    在每个角色的设置页上，有多个可以配置的设置。 这些设置只影响作为**合格**分配（不是**永久**分配）的用户。

## <a name="activations"></a>激活

使用“激活”滑块设置角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可为 1 到 72 小时。

## <a name="notifications"></a>通知

使用“通知”开关指定当角色激活时管理员是否将收到电子邮件通知。 此通知可用于检测未经授权或非法激活。

设置为“启用”时，通知将发送至：

- 特权角色管理员
- 安全管理员
- 全局管理员

有关详细信息，请参阅[Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)。

## <a name="incidentrequest-ticket"></a>事件/请求票证

使用 "**事件/请求票证**" 开关来要求符合条件的管理员在激活其角色时包括票证编号。 这种做法可以使角色访问审核更为有效。

## <a name="multi-factor-authentication"></a>多因素身份验证

使用“多重身份验证”开关指定是否要求用户在激活其角色之前，先使用 MFA 验证其身份。 它们只需在每个会话中验证其标识一次，而不是每次都激活角色。 启用 MFA 时，请记住两点提示：

- Microsoft 帐户的电子邮件地址（通常 @outlook.com，但并不总是）的用户无法注册 Azure 多重身份验证。 如果要将角色分配给具有 Microsoft 帐户的用户，则应将其设置为永久管理员或禁用该角色的多重身份验证。
- 对于 Azure AD 和 Office 365 的高特权角色，无法禁用 Azure 多重身份验证。 此安全功能有助于保护下列角色：  
  
  - Azure 信息保护管理员
  - 计费管理员
  - 云应用程序管理员
  - 法规管理员
  - 条件访问管理员
  - Dynamics 365 管理员
  - 客户密码箱访问审批人
  - 目录写入者
  - Exchange 管理员
  - 全局管理员
  - Intune 管理员
  - Power BI 管理员
  - 特权角色管理员
  - 安全管理员
  - SharePoint administrator
  - Skype for Business 管理员
  - 用户管理员

有关详细信息，请参阅[多重身份验证和 Privileged Identity Management](pim-how-to-require-mfa.md)。

## <a name="require-approval"></a>需要审批

如果要委派所需的审批以激活角色，请执行以下步骤。

1. 将“要求批准”设置为“启用”。 该窗格扩展选项以选择审批者。

    ![Azure AD 角色-设置-需要批准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    如果未指定任何审批者，特权角色管理员将成为默认审批者，并需要审批此角色的所有激活请求。

1. 若要添加审批者，请单击 "**选择审批者**"。

    ![Azure AD 角色-设置-需要批准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 选择 "特权角色管理员" 之外的一个或多个审批者，然后单击 "**选择**"。 建议至少添加两个审批者。 即使将自己添加为审批者，也无法自行批准角色激活。 所选项将出现在所选审批者列表中。

1. 指定所有角色设置后，请选择 "**保存**" 以保存所做的更改。

---

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [为 Privileged Identity Management 中 Azure AD 角色配置安全警报](pim-how-to-configure-security-alerts.md)
