---
title: "如何开始进行访问权限审查 | Microsoft 文档"
description: "了解如何使用 Azure Privileged Identity Management 应用程序为特权标识创建访问审阅。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 3e52b731-55f4-4c8a-ba87-9fd34033f52f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8c9bac62a92947ea888f1ccdbd300f5b40cb3e07
ms.contentlocale: zh-cn
ms.lasthandoff: 12/29/2016


---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中开始访问审阅
当用户不再需要拥有的特权访问时，角色分配将变为“过时”。 为了降低与这些过时角色分配相关联的风险，特权角色管理员应定期审阅用户已分配的角色。 本文档包含在 Azure AD Privileged Identity Management (PIM) 中开始访问审阅的相关步骤。

## <a name="start-an-access-review"></a>开始访问审阅
> [!NOTE]
> 如果尚未在 Azure 门户中向你的仪表板添加 PIM 应用程序，请参阅 [Azure Privileged Identity Management 入门](active-directory-privileged-identity-management-getting-started.md)中的步骤
> 
> 

从 PIM 应用程序主页面中，可通过三种方式开始访问审阅：

* “访问审阅” > “添加”
* “角色” > “审阅”按钮
* 依次选择要从角色列表中审阅的特定角色 >“审阅”按钮

单击“审阅”按钮时，将显示“开始访问审阅边栏选项卡。 在此边栏选项卡上，你将使用某个名称在限制时间内配置审阅、选择要审阅的角色，并确定谁将执行审阅。

![开始访问审阅 - 屏幕截图][1]

### <a name="configure-the-review"></a>配置审阅
若要创建访问审阅，你需要为其命名并设置开始日期和结束日期。

![配置审阅 - 屏幕截图][2]

设置足够长的审阅时间，以便用户完成审阅。 如果要在结束日期之前完成，你始终可以提前停止该审阅。

### <a name="choose-a-role-to-review"></a>选择要审阅的角色
每个审阅仅侧重于一个角色。 除非已从特定角色边栏选项卡开始访问审阅，否则需要立即选择一个角色。

1. 导航到**审阅角色成员身份**
   
    ![审阅角色成员身份 - 屏幕截图][3]
2. 从列表中选择一个角色。

### <a name="decide-who-will-perform-the-review"></a>确定谁将执行审阅
有三个选项用于执行审阅。 你可以将审阅分配给其他人完成、自行执行，也可以让每个用户审阅自己的访问。

1. 导航到**选择审阅者**
   
    ![选择审阅者 - 屏幕快照][4]
2. 选择以下选项之一：
   
   * **选择审阅者**：如果你不知道谁需要访问，请使用此选项。 使用此选项，可以将审阅分配给资源所有者或组管理员完成。
   * **我**：如果你想要预览访问审阅的工作原理，或者你想要代表无法执行此操作的用户进行审阅，此选项很有用。
   * **成员审阅本身**：使用此选项让用户审阅自己的角色分配。

### <a name="start-the-review"></a>开始审阅
最后，如果用户批准其访问，你可以选择要求他们提供原因。 添加审阅的描述（如果愿意）并选择“开始”。

确保让你的用户知道他们有一个等待中的访问审阅，并向他们显示[如何执行访问审阅](active-directory-privileged-identity-management-how-to-perform-security-review.md)。

## <a name="manage-the-access-review"></a>管理访问审阅
当审阅者在 Azure AD PIM 仪表板中完成其审阅时，你可以在访问审阅部分中跟踪进度。 在[审阅完成](active-directory-privileged-identity-management-how-to-complete-review.md)之前，目录中的任何访问权限都不会更改。

在审阅期限结束之前，你都可以提醒用户完成其审阅，或者从访问审阅部分提前结束审阅。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>PIM 目录
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png

