---
title: Azure AD PIM 中的电子邮件通知 | Microsoft Docs
description: 介绍 Azure AD Privileged Identity Management (PIM) 中的电子邮件通知
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: ffac7768fefece24cb69789558ce500e1ff64d98
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257884"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Azure AD PIM 中的电子邮件通知

当 Azure AD Privileged Identity Management (PIM) 中发生关键事件时，电子邮件通知将发送给相关管理员或用户。 例如，PIM 会针对以下事件发送电子邮件：

- 当特权角色激活时正在等待审批时
- 当特权角色激活请求已批准时
- 当特权角色已激活时
- 当特权角色已分配时
- 当 Azure AD PIM 已启用时

从 2018 年 7 月底开始，通过 PIM 发送的电子邮件通知将具有新的发件人电子邮件地址和新的可视化设计。 此更新将影响用于 Azure AD 的 PIM 和用于 Azure 资源的 PIM。 之前触发电子邮件通知的所有事件将继续发送电子邮件。 一些电子邮件将更新内容，提供更有针对性的信息。

## <a name="sender-email-address"></a>发件人电子邮件地址

从 2018 年 7 月底开始，电子邮件通知具有以下地址：

- 电子邮件地址：**azure-noreply@microsoft.com**
- 显示名称：Microsoft Azure

以前，电子邮件通知具有以下地址：

- 电子邮件地址：**azureadnotifications@microsoft.com**
- 显示名称：Microsoft Azure AD 通知服务

## <a name="email-subject-line"></a>电子邮件主题行

从 2018 年 7 月底开始，Azure AD 和 Azure 资源角色的电子邮件通知将在主题行中带有一个 PIM 前缀。 下面是一个示例：

- PIM：已为 Alain Charon 永久分配备份读取器角色。

## <a name="pim-emails-for-azure-ad-roles"></a>用于 Azure AD 角色的 PIM 电子邮件

从 2018 年 7 月底开始，用于 Azure AD 角色的 PIM 电子邮件通知具有更新的设计。 下面显示了当用户激活虚构 Contoso 组织的特权角色时发送的示例电子邮件。

![用于 Azure AD 角色的新 PIM 电子邮件](./media/pim-email-notifications/email-directory-new.png)

以前，当用户激活了特权角色时，电子邮件如下所示。

![用于 Azure AD 角色的旧 PIM 电子邮件](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>用于 Azure 资源角色的 PIM 电子邮件

从 2018 年 7 月底开始，用于 Azure 资源角色的 PIM 电子邮件通知具有更新的设计。 下面显示了当为用户分配了虚构 Contoso 组织的特权角色时发送的示例电子邮件。

![用于 Azure 资源角色的新 PIM 电子邮件](./media/pim-email-notifications/email-resources-new.png)

以前，当为用户分配了特权角色时，电子邮件如下所示。

![用于 Azure 资源角色的旧 PIM 电子邮件](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>后续步骤

- [如何在 Azure AD PIM 中管理角色激活设置](pim-how-to-change-default-settings.md)
- [Azure AD PIM 中的审批](azure-ad-pim-approval-workflow.md)
