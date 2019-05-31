---
title: 在 PIM 的 Azure Active Directory 中配置 Azure AD 角色设置 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中配置 Azure AD 角色设置。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/31/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bc7d3ffcb56251825bf5f6d760de647938f1ead
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417870"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>在 PIM 中配置 Azure AD 角色设置

特权的角色管理员可以自定义 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 在其组织中，包括更改激活合格角色分配的用户体验。

## <a name="open-role-settings"></a>打开角色设置

请按照下列步骤以打开 Azure AD 角色的设置。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure AD 角色”。 

1. 单击“设置”  。

    ![Azure AD 角色的设置](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. 单击“角色”。 

1. 单击要配置其设置的角色。

    ![Azure AD 角色的设置角色](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    在每个角色的设置页上，有多个可以配置的设置。 这些设置只影响作为**合格**分配（不是**永久**分配）的用户。

## <a name="activations"></a>激活

使用“激活”  滑块设置角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可为 1 到 72 小时。

## <a name="notifications"></a>通知

使用“通知”开关指定当角色激活时管理员是否将收到电子邮件通知  。 这对于检测未经授权或非法的激活而言相当有用。

设置为“启用”时，通知将发送至  ：

- 特权角色管理员
- 安全管理员
- 全局管理员角色

有关详细信息，请参阅 [PIM 中的电子邮件通知](pim-email-notifications.md)。

## <a name="incidentrequest-ticket"></a>事件/请求票证

使用“事件/请求票证”  开关指定是否要要求合格管理员在激活其角色时包含票证编号。 执行角色访问审核时，这相当有用。

## <a name="multi-factor-authentication"></a>多重身份验证

使用“多重身份验证”  开关指定是否要求用户在激活其角色之前，先使用 MFA 验证其身份。 他们只需在每个会话中执行身份验证一次，而无需在每次激活角色时都执行身份验证。 启用 MFA 时，请记住两点提示：

* 使用 Microsoft 帐户作为电子邮件地址（通常是 @outlook.com，但不一定）的用户无法注册 Azure MFA。 如果想要将角色分配到使用 Microsoft 帐户的用户，应将其设置为永久管理员，或者为该角色禁用 MFA。
* 无法对 Azure AD 和 Office365 中的高特权角色禁用 MFA。 这是一项安全功能，因为这些角色应该受到严密的保护：  
  
  * Azure 信息保护管理员
  * 计费管理员
  * 云应用管理员
  * 符合性管理员
  * 条件访问管理员
  * CRM 服务管理员
  * 客户密码箱访问审批者
  * 目录编写人员
  * Exchange 管理员
  * 全局管理员角色
  * Intune 服务管理员
  * Power BI 服务管理员
  * 特权角色管理员
  * 安全管理员
  * SharePoint 服务管理员
  * Skype for Business 管理员
  * 用户管理员

有关详细信息，请参阅[多重身份验证 (MFA) 和 PIM](pim-how-to-require-mfa.md)。

## <a name="require-approval"></a>需要审批

如果要求批准以激活角色，请按照以下步骤操作。

1. 将“要求批准”  设置为“启用”  。 该窗格扩展选项以选择审批者。

    ![Azure AD 角色-设置-需要批准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    如果**没有**指定任何审批者，则特权角色管理员将成为默认审批者。 审批此角色的**所有**激活请求时都需要特权角色管理员。

1. 若要指定审批者，请单击“选择审批者”  。

    ![Azure AD 角色-设置-需要批准](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 选择一个或多个审批者，然后单击“选择”  。 可以选择用户或组。 建议至少选择 2 个审批者。 不允许自审批。

    所选项将出现在所选审批者列表中。

1. 在指定所有角色设置后，单击“保存”以保存更改。 


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>后续步骤

- [将 Azure AD PIM 中的角色分配](pim-how-to-add-role-to-user.md)
- [在 PIM 中配置 Azure AD 角色的安全警报](pim-how-to-configure-security-alerts.md)
