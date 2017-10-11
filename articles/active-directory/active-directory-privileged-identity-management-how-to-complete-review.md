---
title: "如何完成访问查看 |Microsoft 文档"
description: "Azure AD 特权标识管理中启动访问查看后，了解如何完成它，并查看结果"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: ca2a1c7c287e4cf6b1b50cfb0068861b6f525596
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>如何完成 Azure AD 特权标识管理中的访问查看
特权的角色管理员可以一次查看特权的访问[已启动安全检查](active-directory-privileged-identity-management-how-to-start-security-review.md)。 Azure AD 特权标识管理 (PIM) 会自动发送一封电子邮件，提示用户审查其访问权限。 如果用户没有获得一封电子邮件，你可以向他们发送说明[如何执行安全检查](active-directory-privileged-identity-management-how-to-perform-security-review.md)。

在安全考核期内已结束，或所有用户都完成其自查看后，请按照本文管理评审并查看结果中的步骤。

## <a name="manage-security-reviews"></a>管理安全评审
1. 转到[Azure 门户](https://portal.azure.com/)和选择**Azure AD Privileged Identity Management**应用程序在你的仪表板上。
2. 选择**访问评审**仪表板部分。
3. 选择你想要管理的访问评审。

访问查看详细信息边栏选项卡上有用于管理该评审数字选项。

![PIM 访问查看按钮-屏幕截图][1]

### <a name="remind"></a>提醒
如果访问查看设置，以便用户查看本身，**提醒**按钮发出通知。 

### <a name="stop"></a>停止
所有访问评审都设置结束日期，但你可以使用**停止**早完成的按钮。 如果尚未按此时间评审任何用户，他们将不能向后停止评审。 已停止后，您无法重新启动检查。

### <a name="apply"></a>应用
访问查看完成后，或者因为你达到结束日期或者是手动，停止**应用**按钮实现的评审的结果。 如果在评审会议上，用户的访问被拒绝，这是将删除其角色分配的步骤。  

### <a name="export"></a>导出
如果你想要手动应用安全评审的结果，你可以导出评审。 **导出**按钮将开始下载 CSV 文件。 你可以管理 Excel 或其他程序打开 CSV 文件中的结果。

### <a name="delete"></a>删除
如果你不感兴趣任何进一步的评审，请将其删除。 **删除**按钮从 PIM 应用程序中移除评审。

> [!IMPORTANT]
> 你不将删除之前会出现一个警告，因此请确保你想要删除该检查。 

## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
