---
title: PIM 中的电子邮件通知 - Azure Active Directory | Microsoft Docs
description: 介绍 Azure AD Privileged Identity Management (PIM) 中的电子邮件通知。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02fbfc83c16cb13376cce820f19b247a7cd7db59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82232302"
---
# <a name="email-notifications-in-pim"></a>PIM 中的电子邮件通知

可以通过 Privileged Identity Management (PIM) 了解 Azure Active Directory (Azure AD) 组织中何时发生重要事件，例如何时分配或激活角色。 Privileged Identity Management 通过向你和其他参与者发送电子邮件通知来让你随时了解情况。 这些电子邮件可能还包含指向相关任务的链接，比如激活或续订角色。 本文介绍了这些电子邮件的外观、何时发送电子邮件，以及接收人员。

## <a name="sender-email-address-and-subject-line"></a>发件人电子邮件地址和主题行

从 Privileged Identity Management 针对 Azure AD 和 Azure 资源角色发送的电子邮件具有以下发件人电子邮件地址：

- 电子邮件地址：**azure-noreply\@microsoft.com**
- 显示名称：Microsoft Azure

这些电子邮件在主题行中包括 PIM 前缀。 下面是一个示例：

- PIM：已为 Alain Charon 永久分配备份读取器角色

## <a name="notifications-for-azure-ad-roles"></a>Azure AD 角色的通知

当 Azure AD 角色发生以下事件时，Privileged Identity Management 将发送电子邮件：

- 当特权角色激活时正在等待审批时
- 当特权角色激活请求已完成时
- 当启用 Azure AD Privileged Identity Management 时

接收这些 Azure AD 角色电子邮件的用户取决于角色、事件和通知设置：

| User | 角色激活正在等待审批 | 角色激活请求已完成 | PIM 已启用 |
| --- | --- | --- | --- |
| 特权角色管理员</br>（激活/符合条件） | 是</br>（仅当未指定明确审批者） | 是* | 是 |
| 安全管理员</br>（激活/符合条件） | 否 | 是* | 是 |
| 全局管理员角色</br>（激活/符合条件） | 否 | 是* | 是 |

\* 如果[“通知”设置](pim-how-to-change-default-settings.md#notifications)设置为“启用”。

下面显示了当用户激活虚构 Contoso 组织的 Azure AD 角色时发送的示例电子邮件。

![Azure AD 角色的新 Privileged Identity Management 电子邮件](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Azure AD 角色的每周 Privileged Identity Management 摘要电子邮件

Azure AD 角色的每周 Privileged Identity Management 摘要电子邮件将发送给启用了 Privileged Identity Management 的特权角色管理员、安全管理员和全局管理员。 此每周电子邮件提供一周的 Privileged Identity Management 活动快照以及特权角色分配。 它仅适用于公有云上的 Azure AD 组织。 下面是电子邮件示例：

![Azure AD 角色的每周 Privileged Identity Management 摘要电子邮件](./media/pim-email-notifications/email-directory-weekly.png)

电子邮件包括四个磁贴：

| 磁贴 | 说明 |
| --- | --- |
| **已激活的用户** | 用户在组织内激活其符合条件角色的次数。 |
| **永久用户** | 用户符合资格的分配被设定为永久分配的次数。 |
| **Privileged Identity Management 中的角色分配** | 在 Privileged Identity Management 中为用户分配符合条件的角色的次数。 |
| **PIM 之外的角色分配** | 在 Privileged Identity Management 外部（在 Azure AD 内部）为用户分配永久角色的次数。 |

“热门角色概述”部分根据每个角色的永久和符合条件管理员的总数列出了组织中最热门的五个角色。 采取措施链接打开 [PIM 向导](pim-security-wizard.md)，可以将永久管理员批量转换为符合条件的管理员。

## <a name="email-timing-for-activation-approvals"></a>激活审批的电子邮件发送时机

当用户激活其角色且角色设置需要审批时，审批者每次审批会收到三封电子邮件：

- 请求批准或拒绝用户的激活请求（由请求批准引擎发送）
- 已批准用户请求（由请求批准引擎发送）
- 已激活用户角色（由 Privileged Identity Management 发送）

请求批准引擎发送的前两封电子邮件可以延迟。 目前，90% 的电子邮件需要三到十分钟才能送达，但是对于 1% 的客户来说，可能需要更长时间，最多十五分钟。

如果在发送第一封电子邮件之前在 Azure 门户中批准了审批请求，则将不再触发第一封电子邮件，并且不会通过电子邮件将审批请求通知其他审批者。 这可能会表现为这些审批者没有收到电子邮件，但这是预期行为。

## <a name="pim-emails-for-azure-resource-roles"></a>用于 Azure 资源角色的 PIM 电子邮件

当 Azure 资源角色发生以下事件时，Privileged Identity Management 会向所有者和用户访问管理员发送电子邮件：

- 角色分配正在等待审批时
- 分配角色时
- 角色即将到期
- 角色有资格扩展
- 最终用户续订角色
- 角色激活请求已完成

当 Azure 资源角色发生以下事件时，Privileged Identity Management 会向最终用户发送电子邮件：

- 向用户分配角色时
- 用户角色已过期
- 用户角色已扩展
- 用户角色激活请求已完成

下面显示了当为用户分配了虚构 Contoso 组织的 Azure 资源角色时发送的示例电子邮件。

![Azure 资源角色的新 Privileged Identity Management 电子邮件](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure AD 角色的请求](azure-ad-pim-approval-workflow.md)
