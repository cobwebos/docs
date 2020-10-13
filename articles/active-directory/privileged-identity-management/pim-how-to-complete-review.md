---
title: 在 PIM 中完成对 Azure AD 角色的访问评审 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中完成 Azure AD 角色的访问评审并查看结果
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742210"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中完成对 Azure AD 角色的访问评审

[访问评审开始](pim-how-to-start-security-review.md)后，特权角色管理员可以评审特权访问。  Privileged Identity Management (PIM) 会自动向 Azure Active Directory (Azure AD) 组织中的用户发送一封电子邮件，提示他们查看其访问权限。 如果用户未收到电子邮件，可以向他们发送[如何执行访问评审](pim-how-to-perform-security-review.md)的相关说明。

访问评审期限结束，或者所有用户已完成其自评审后，请按照本文中的步骤管理评审并查看结果。

## <a name="manage-access-reviews"></a>管理访问评审

1. 转到 [Azure 门户](https://portal.azure.com/)，并在仪表板上选择“Azure AD Privileged Identity Management”  服务。
1. 选择仪表板的“访问审阅”  部分。
1. 选择要管理的访问审阅。

在访问审阅的详细信息边栏选项卡上，有大量用于管理该审阅的选项。

![Privileged Identity Management 访问评审按钮 - 屏幕截图](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>提醒

如果设置了用于用户审阅自身的访问审阅，“提醒”  按钮将发送一条通知。

### <a name="stop"></a>停止

所有访问审阅都有结束日期，但可以使用“停止”  按钮提前结束。 如果此时还有未审阅的用户，他们在停止审阅后将无法再得到审阅。 停止后，无法重新开始审阅。

### <a name="apply"></a>应用

结束访问审阅后，“应用”  按钮将实现审阅结果，因为结束日期已到或已手动停止它。 如果在审阅中拒绝了用户的访问，在此步骤中将删除其角色分配。  

### <a name="export"></a>Export

如果要手动应用访问评审的结果，可以导出该评审。 “导出”  按钮将开始下载 CSV 文件。 可以在 Excel 或可打开 CSV 文件的其他程序中管理结果。

### <a name="delete"></a>删除

如果不想要进一步了解审阅，请将其删除。 “删除”按钮  可从 Privileged Identity Management 服务中删除评审。

> [!IMPORTANT]
> 不需确认此破坏性更改，因此请验证是否要删除该评审。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中开始对 Azure AD 角色的访问评审](pim-how-to-start-security-review.md)
- [在 Privileged Identity Management 中执行对我的 Azure AD 角色的访问评审](pim-how-to-perform-security-review.md)
