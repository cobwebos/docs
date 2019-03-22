---
title: PIM 中的电子邮件通知 - Azure | Microsoft Docs
description: 介绍 Azure AD Privileged Identity Management (PIM) 中的电子邮件通知。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f2e5674413cb4057187b7573fde0bcd8899caab
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57905165"
---
# <a name="email-notifications-in-pim"></a>PIM 中的电子邮件通知

可以通过 Azure AD Privileged Identity Management (PIM) 了解重要事件何时发生，例如何时分配或激活角色。 PIM 向你和其他参与者发送电子邮件通知，以便随时了解状况。 这些电子邮件可能还包含指向相关任务的链接，比如激活或续订角色。 本文介绍了这些电子邮件的外观、何时发送电子邮件，以及接收人员。

## <a name="sender-email-address-and-subject-line"></a>发件人电子邮件地址和主题行

针对 Azure AD 和 Azure 资源角色从 PIM 发送的电子邮件具有以下发件人电子邮件地址：

- 电子邮件地址： **azure noreply\@microsoft.com**
- 显示名称：Microsoft Azure

这些电子邮件在主题行中包括 PIM 前缀。 下面是一个示例：

- PIM：已为 Alain Charon 永久分配备份读取器角色

## <a name="pim-emails-for-azure-ad-roles"></a>用于 Azure AD 角色的 PIM 电子邮件

当发生以下事件，PIM 会向 Azure AD 角色发送电子邮件：

- 当特权角色激活时正在等待审批时
- 当特权角色激活请求已完成时
- 当 Azure AD PIM 已启用时

接收这些 Azure AD 角色电子邮件的用户取决于角色、事件和通知设置：

| 用户 | 角色激活正在等待审批 | 角色激活请求已完成 | PIM 已启用 |
| --- | --- | --- | --- |
| 特权角色管理员</br>（激活/符合条件） | 是</br>（仅当未指定明确审批者） | 是* | 是 |
| 安全管理员</br>（激活/符合条件） | 否 | 是* | 是 |
| 全局管理员角色</br>（激活/符合条件） | 否 | 是* | 是 |

\* 如果[“通知”设置](pim-how-to-change-default-settings.md#notifications)设置为“启用”。

下面显示了当用户激活虚构 Contoso 组织的 Azure AD 角色时发送的示例电子邮件。

![用于 Azure AD 角色的新 PIM 电子邮件](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Azure AD 角色的每周 PIM 摘要电子邮件

Azure AD 角色的 PIM 摘要电子邮件将发送给启用了 PIM 的特权角色管理员、安全管理员和全局管理员。 此每周电子邮件提供一周的 PIM 活动快照以及特权角色分配。 它仅适用于公有云上的租户。 下面是电子邮件示例：

![Azure AD 角色的每周 PIM 摘要电子邮件](./media/pim-email-notifications/email-directory-weekly.png)

电子邮件包括四个磁贴：

| 磁贴 | 描述 |
| --- | --- |
| **已激活的用户** | 用户在租户内激活其符合条件角色的次数。 |
| **永久用户** | 用户符合资格的分配被设定为永久分配的次数。 |
| **PIM 中的角色分配** | 在 PIM 中为用户分配符合条件的角色的次数。 |
| **PIM 之外的角色分配** | 在 PIM 之外（Azure AD 内部）为用户分配永久角色的次数。 |

**热门角色概述**部分根据每个角色的永久和符合条件管理员的总数列出了租户中的前五个角色。 采取措施链接打开 [PIM 向导](pim-security-wizard.md)，可以将永久管理员批量转换为符合条件的管理员。

## <a name="pim-emails-for-azure-resource-roles"></a>用于 Azure 资源角色的 PIM 电子邮件

当 Azure 资源角色发生以下事件，PIM 会向所有者和用户访问管理员发送电子邮件：

- 角色分配正在等待审批时
- 分配角色时
- 角色即将到期
- 角色有资格扩展
- 最终用户续订角色
- 角色激活请求已完成

当 Azure 资源角色发生以下事件，PIM 会向最终用户发送电子邮件：

- 向用户分配角色时
- 用户角色已过期
- 用户角色已扩展
- 用户角色激活请求已完成

下面显示了当为用户分配了虚构 Contoso 组织的 Azure 资源角色时发送的示例电子邮件。

![用于 Azure 资源角色的新 PIM 电子邮件](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中配置 Azure AD 目录角色设置](pim-how-to-change-default-settings.md)
- [在 PIM 中批准或拒绝 Azure AD 目录角色的请求](azure-ad-pim-approval-workflow.md)
