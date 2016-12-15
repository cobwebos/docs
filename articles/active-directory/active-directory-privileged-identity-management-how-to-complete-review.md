---
title: "如何完成访问权限审查 | Microsoft 文档"
description: "在 Azure AD Privileged Identity Management 中开始访问审阅后，了解如何结束它并查看结果"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/30/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a5a9d2aeb5d9d13d0561e21ea90160511659d6a3


---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中结束访问审阅
[开始安全审阅](active-directory-privileged-identity-management-how-to-start-security-review.md)后，特权角色管理员可以审阅特权访问。 Azure AD Privileged Identity Management (PIM) 将自动发送一封提示用户审阅其访问的电子邮件。 如果用户未收到电子邮件，你可以向他们发送[如何执行安全审阅](active-directory-privileged-identity-management-how-to-perform-security-review.md)中的说明。

安全审阅期限结束，或者所有用户已完成其自审阅后，按照本文中的步骤管理审阅并查看结果。

## <a name="manage-security-reviews"></a>管理安全审阅
1. 转到 [Azure 门户](https://portal.azure.com/)，然后在仪表板上选择“Azure AD Privileged Identity Management”应用程序。
2. 选择仪表板的“访问审阅”部分。
3. 选择要管理的访问审阅。

在访问审阅的详细信息边栏选项卡上，有大量用于管理该审阅的选项。

![PIM 访问审阅按钮 - 屏幕截图][1]

### <a name="remind"></a>提醒
如果设置了用于用户审阅自身的访问审阅，“提醒”按钮将发送一条通知。 

### <a name="stop"></a>停止
所有访问审阅都有结束日期，但你可以使用“停止”按钮提前结束。 如果此时尚未审阅任何用户，在停止审阅后不会执行审阅。 停止后，无法重新开始审阅。

### <a name="apply"></a>应用
结束访问审阅后，“应用”按钮将实现审阅结果，因为结束日期已到或已手动停止它。 如果在审阅中拒绝了用户的访问，在此步骤中将删除其角色分配。  

### <a name="export"></a>导出
如果要手动应用安全审阅的结果，可以导出该审阅。 “导出”按钮将开始下载 CSV 文件。 可以在 Excel 或可打开 CSV 文件的其他程序中管理结果。

### <a name="delete"></a>删除
如果你不想要进一步了解审阅，请将其删除。 “删除”按钮可从 PIM 应用程序中删除审阅。

> [!IMPORTANT]
> 在发生删除前不会收到任何警告，因此请确保你要删除该审阅。
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png



<!--HONumber=Nov16_HO3-->


