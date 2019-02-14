---
title: 在 PIM 中启动 Azure AD 目录角色的访问评审 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中启动 Azure AD 目录角色的访问评审。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33f2e3249d1b7ad0efc16dd0b9ced26379c3cae7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174463"
---
# <a name="start-an-access-review-for-azure-ad-directory-roles-in-pim"></a>在 PIM 中启动 Azure AD 目录角色的访问评审
当用户有不再需要的特权访问时，角色分配将变为“过时”。 为了降低与这些过时角色分配相关联的风险，特权角色管理员或全局管理员应定期创建访问评审来让管理员审阅用户的已分配角色。 本文档包含在 Azure AD Privileged Identity Management (PIM) 中开始访问审阅的相关步骤。

## <a name="start-an-access-review"></a>开始访问审阅
> [!NOTE]
> 如果尚未在 Azure 门户中向仪表板添加 PIM 应用程序，请参阅 [Azure Privileged Identity Management 入门](pim-getting-started.md)中的步骤
> 
> 

从 PIM 应用程序主页面中，可通过三种方式开始访问审阅：

* “访问审阅” > “添加”
* “角色” > “审阅”按钮
* 从角色列表中选择要审阅的特定角色，然后选择“审阅”按钮

单击“审阅”按钮时，会显示“开始访问审阅边栏选项卡。 在此边栏选项卡上，将使用某个名称在限制时间内配置审阅、选择要审阅的角色，并确定谁将执行审阅。

![开始访问审阅 - 屏幕截图](./media/pim-how-to-start-security-review/PIM_start_review.png)

### <a name="configure-the-review"></a>配置审阅
要创建访问审阅，需要为其命名并设置开始日期和结束日期。

![配置审阅 - 屏幕截图](./media/pim-how-to-start-security-review/PIM_review_configure.png)

设置足够长的审阅时间，以便用户完成审阅。 如果要在结束日期之前完成，始终可以提前停止该审阅。

### <a name="choose-a-role-to-review"></a>选择要审阅的角色
每个审阅仅关注一个角色。 除非已从特定角色边栏选项卡开始访问审阅，否则此时需要选择一个角色。

1. 导航到**审阅角色成员身份**
   
    ![审阅角色成员身份 - 屏幕截图](./media/pim-how-to-start-security-review/PIM_review_role.png)
2. 从列表中选择一个角色。

### <a name="decide-who-will-perform-the-review"></a>确定谁将执行审阅
有三个选项用于执行审阅。 可以将审阅分配给其他人完成、自行执行，也可以让每个用户审阅自己的访问。

1. 导航到**选择审阅者**
   
    ![选择审阅者 - 屏幕快照](./media/pim-how-to-start-security-review/PIM_review_reviewers.png)
2. 选择以下选项之一：
   
   * **选择评审者**：如果不知道谁需要访问，请使用此选项。 使用此选项，可以将审阅分配给资源所有者或组管理员完成。
   * **我**：如果想要预览访问评审的工作原理，或者想要代表无法执行此操作的用户进行评审，此选项很有用。
   * **成员自我评审**：使用此选项让用户评审自己的角色分配。

### <a name="start-the-review"></a>开始审阅
最后，如果用户批准其访问，可以选择要求他们提供原因。 添加审阅的描述（如果愿意）并选择“开始”。

确保让用户知道他们有一个等待中的访问审阅，并向他们显示[如何执行访问审阅](pim-how-to-perform-security-review.md)。

## <a name="manage-the-access-review"></a>管理访问审阅
当审阅者在 Azure AD PIM 仪表板中完成其审阅时，可以在访问审阅部分中跟踪进度。 在[审阅完成](pim-how-to-complete-review.md)之前，目录中的任何访问权限都不会更改。

在审阅期限结束之前，都可以提醒用户完成其审阅，或者从访问审阅部分提前结束审阅。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤

- [在 PIM 中完成 Azure AD 目录角色的访问评审](pim-how-to-complete-review.md)
- [在 PIM 中对 Azure AD 目录角色执行访问评审](pim-how-to-perform-security-review.md)
- [在 PIM 中启动 Azure 资源角色的访问评审](pim-resource-roles-start-access-review.md)
