---
title: 在 PIM 中配置 Azure AD 目录角色设置 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中配置 Azure AD 目录角色设置。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 20a704a0d5b61134a61b5cbf02a1c71dbc7039e1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189331"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>在 PIM 中配置 Azure AD 目录角色设置

特权角色管理员可以自定义其组织中的 Azure AD Privileged Identity Management (PIM)，包括更改激活合格角色分配的用户的体验。

## <a name="open-role-settings"></a>打开角色设置

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 目录角色”。

1. 单击“设置”。

    ![Azure AD 目录角色 - 设置](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. 单击“角色”。

1. 单击要配置其设置的角色。

    ![Azure AD 目录角色 - 设置角色](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    在每个角色的设置页上，有多个可以配置的设置。 这些设置只影响作为**合格**分配（不是**永久**分配）的用户。

## <a name="activations"></a>激活

**激活**滑块是角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可为 1 到 72 小时。

## <a name="notifications"></a>通知

使用**通知**开关，可以选择是否要让系统发送电子邮件给管理员来确认他们已激活角色。 这对于检测未经授权或非法的激活而言相当有用。

## <a name="incidentrequest-ticket"></a>事件/请求票证

使用**事件/请求票证**开关，可以选择是否要要求合格管理员在激活其角色时包含票证编号。 执行角色访问审核时，这相当有用。

## <a name="multi-factor-authentication"></a>多重身份验证

使用**多重身份验证**开关：可以选择是否要求用户在激活其角色之前，先使用 MFA 验证其身份。 他们只需在每个会话中执行身份验证一次，而无需在每次激活角色时都执行身份验证。 启用 MFA 时，请记住两点提示：

* 使用 Microsoft 帐户作为电子邮件地址（通常是 @outlook.com，但不一定）的用户无法注册 Azure MFA。 如果想要将角色分配到使用 Microsoft 帐户的用户，应将其设置为永久管理员，或者为该角色禁用 MFA。
* 无法对 Azure AD 和 Office365 中的高特权角色禁用 MFA。 这是一项安全功能，因为这些角色应该受到严密的保护：  
  
  * 应用程序管理员
  * 应用程序代理服务器管理员
  * 计费管理员  
  * 法规管理员  
  * CRM 服务管理员
  * 客户密码箱访问审批人
  * 目录写入者  
  * Exchange 管理员  
  * 全局管理员
  * Intune 服务管理员
  * 邮箱管理员  
  * 合作伙伴一线支持人员  
  * 合作伙伴二线支持人员  
  * 特权角色管理员
  * 安全管理员  
  * SharePoint 管理员  
  * Skype for Business 管理员  
  * 用户帐户管理员  

有关将 MFA 与 PIM 配合使用的详细信息，请参阅[在 PIM 中要求 Azure AD 目录角色进行多重身份验证](pim-how-to-require-mfa.md)。

## <a name="require-approval"></a>需要审批

使用**需要审批**开关，可以选择在激活此角色时是否要求进行审批。

1. 将此开关设置为**启用**时，窗格会展开并显示用来选择审批者的选项。

    ![Azure AD 目录角色 - 设置 - 需要审批](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    如果**没有**指定任何审批者，则特权角色管理员将成为默认审批者。 审批此角色的**所有**激活请求时都需要特权角色管理员。

1. 若要指定审批者，请单击“选择审批者”。

    ![Azure AD 目录角色 - 设置 - 需要审批](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 选择一个或多个审批者，然后单击“选择”。 可以选择用户或组。 建议至少选择 2 个审批者。 不允许自审批。

    所选项将出现在所选审批者列表中。

1. 在指定所有角色设置后，单击“保存”以保存更改。


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>后续步骤

- [在 PIM 中要求 Azure AD 目录角色进行多重身份验证](pim-how-to-require-mfa.md)
- [在 PIM 中为 Azure AD 目录角色配置安全警报](pim-how-to-configure-security-alerts.md)
