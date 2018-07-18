---
title: 如何管理角色激活设置 | Microsoft 文档
description: 了解如何使用 Azure Active Directory Privileged Identity Management 扩展更改特权标识的默认设置。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 13e00ac57bfc867ad13ae3d7958fca37eaf5beee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723249"
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中管理角色激活设置
特权角色管理员可以自定义其组织中的 Azure AD Privileged Identity Management (PIM)，包括更改激活合格角色分配的用户的体验。

## <a name="manage-the-role-activation-settings"></a>管理角色激活设置
1. 转到 [Azure 门户](https://portal.azure.com)，并从仪表板中选择“Azure AD Privileged Identity Management”应用。
2. 选择“管理特权角色” > “设置” > “特权角色”。
3. 选择想要管理其设置的角色。

在每个角色的设置页上，有许多可以配置的设置。 这些设置只影响充当合格管理员（不是永久管理员）的用户。

**激活**：角色在过期前保持活动状态的时间（以小时为单位）。 此期限可为 1 到 72 小时。

**通知**：可以选择是否要让系统发送电子邮件给管理员来确认他们已激活角色。 这对于检测未经授权或非法的激活而言相当有用。

**事件/请求票证**：可以选择是否要请求合格管理员在激活其角色时包含票证编号。 执行角色访问审核时，这相当有用。

**多重身份验证**：可以选择是否要求用户在激活其角色之前，先使用 MFA 验证其身份。 他们只需在每个会话中执行身份验证一次，而无需在每次激活角色时都执行身份验证。 启用 MFA 时，请记住两点提示：

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

有关在 PIM 中使用 MFA 的详细信息，请参阅[如何要求使用 MFA](pim-how-to-require-mfa.md)。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

