---
title: 在 PIM 的 Azure Active Directory 中配置 Azure 资源角色设置 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中配置 Azure 资源角色设置。
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
ms.openlocfilehash: 73d42c693fae6b538136d1e8c93094a0ea9e2077
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494861"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>在 PIM 中配置 Azure 资源角色设置

在配置 Azure 资源角色设置时，定义应用于 Azure 资源角色分配，在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 的默认设置。 使用以下步骤配置审批工作流并指定谁可以批准或拒绝请求。

## <a name="open-role-settings"></a>打开角色设置

请遵循以下步骤打开 Azure 资源角色的设置。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure 资源”。

1. 单击要管理的资源，如订阅或管理组。

    ![要管理的 Azure 资源的列表](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. 单击“角色设置”。

    ![角色设置](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 单击要配置其设置的角色。

    ![角色设置详细信息](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. 单击“编辑”以打开“角色设置”窗格。

    ![编辑角色设置](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    在每个角色的“角色设置”窗格上，有多个可以配置的设置。

## <a name="assignment-duration"></a>分配持续时间

配置角色的设置时，可以从用于每种分配类型（合格和活动）的两个分配持续时间选项中进行选择·。 在 PIM 中将成员分配到角色时，这些选项将成为默认的最大持续时间。

可以选择其中一个合格的分配持续时间选项：

| | |
| --- | --- |
| **允许永久的合格分配** | 资源管理员可以分配永久的合格成员身份。 |
| **使合格分配在以下时间后过期** | 资源管理员可以要求所有合格分配都具有指定的开始和结束日期。 |

并且，可以选择其中一个活动分配持续时间选项：

| | |
| --- | --- |
| **允许永久的活动分配** | 资源管理员可以分配永久的活动成员身份。 |
| **使活动分配在以下时间后过期** | 资源管理员可以要求所有活动分配都具有指定的开始和结束日期。 |

> [!NOTE] 
> 资源管理员可续订具有特定结束日期的所有分配。 此外，成员也可启动自助服务请求来[扩展或续订角色分配](pim-resource-roles-renew-extend.md)。

## <a name="require-multi-factor-authentication"></a>需要多重身份验证

PIM 针对两种不同的方案提供了可选的 Azure 多重身份验证 (MFA) 执行。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>要求在活动分配时进行多重身份验证

在某些情况下，你可能希望为成员分配短期（例如，一天）角色。 在这种情况下，不需要分配的成员请求激活。 在这种情况下，PIM 无法在成员使用其角色分配时强制执行 MFA，因为成员从分配之时起已在角色中处于活动状态。

为确保完成分配的资源管理员是其本人，可以通过选中“在活动分配时要求进行多重身份验证”框来对活动分配强制执行 MFA。

### <a name="require-multi-factor-authentication-on-activation"></a>要求在激活时进行多重身份验证

可以要求角色的合格成员在激活之前先运行 MFA。 此过程可确保请求激活的用户一定就是其本人。 强制执行此选项可以在用户帐户可能已遭入侵的情况下保护关键资源。

要在激活前要求合格成员运行 MFA，请选中“在激活时要求进行多重身份验证”框。

有关详细信息，请参阅[多重身份验证 (MFA) 和 PIM](pim-how-to-require-mfa.md)。

## <a name="activation-maximum-duration"></a>最长激活持续时间

使用“最长激活持续时间”滑块是角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可为 1 到 24 小时。

## <a name="require-justification"></a>需要理由

可以要求成员在活动分配或激活时输入理由。 若需要理由，请选中“在活动分配时需要理由”框或“在激活时需要理由”框。

## <a name="require-approval-to-activate"></a>需要批准才能激活

如果要求批准以激活角色，请按照以下步骤操作。

1. 选中“需要批准以激活”复选框。

1. 单击“选择审批者”打开“选择成员或组”窗格。

    ![选择成员或组](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 至少选择一个成员或组，然后单击“选择”。 可以添加任何成员和组的组合。 必须至少选择 1 个审批者。 没有默认的审批者。

    所选项将出现在所选审批者列表中。

1. 在指定所有角色设置后，单击“更新”以保存更改。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
- [在 PIM 中为 Azure 资源角色配置安全警报](pim-resource-roles-configure-alerts.md)
