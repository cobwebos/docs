---
title: "使用 Azure AD 针对有权访问应用程序的组成员或用户创建访问评审 | Microsoft Docs"
description: "了解如何针对有权访问应用程序的组成员或用户创建访问评审。"
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: c2c5aee3db11255be5a6fe405424c2cbb92c5b95
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---

# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>使用 Azure AD 针对组成员或应用程序访问权限创建访问评审

当用户不再需要访问权限时，访问权限分配会“过时”。  为了降低与访问权限分配状态相关的风险，管理员可以通过创建访问评审，要求评审已分配到应用程序的组成员或用户。 [管理用户访问权限](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)和[管理来宾访问权限](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)指南提供了有关这些方案的详细信息。  

## <a name="how-to-create-an-access-review"></a>如何创建访问评审


1. 以全局管理员的身份转到[访问评审页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，并切换到“计划”选项卡。
2. 单击用于保存所要创建的访问评审控制的计划。  始终有一个计划（“默认计划”）存在，也可以创建不同的计划。  例如，可以选择针对每个符合性措施或业务目标创建一个计划。
3. 在计划中单击“控制措施”，再单击“添加”按钮以添加控制措施。
4. 每个访问评审需有一个名称，还可以选择性地为它提供说明。  名称向评审者显示。  
5. 默认情况下，访问评审从创建当日开始，计划在一个月内结束。  可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。
6. 访问评审的对象可以是组的成员，或者已分配到应用程序的用户。  可将访问评审的范围进一步限定为仅评审属于成员（或已分配到应用）的来宾用户，而不是评审属于成员或有权访问应用程序的所有用户。
7. 可以选择一个或多个人来评审范围中的所有用户，或者选择让成员评审他们自己的访问权限。  如果资源是一个组，可以要求组的所有者进行评审。  还可以要求评审者在审批访问权限时提供原因。
8. 最后，单击“开始”。


## <a name="managing-the-access-review"></a>管理访问评审

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。  如果选择不让 Azure AD 发送电子邮件，请务必告诉评审者有一个访问评审任务等待他们完成。  可以向评审者显示有关[如何评审访问权限](active-directory-azure-ad-controls-perform-access-review.md)的说明，或者，如果评审工作是让来宾评审他们自己的访问权限，则可以显示有关[如何评审自己的访问权限](active-directory-azure-ad-controls-perform-access-review.md)的说明。

请注意，如果某些评审者是来宾，则仅当他们已接受邀请时，才会通过电子邮件向他们发出通知。


当评审者在 Azure AD 仪表板中完成其评审时，你可以在访问评审部分中跟踪进度。 在[审阅完成](active-directory-azure-ad-controls-complete-access-review.md)之前，目录中的任何访问权限都不会更改。

## <a name="next-steps"></a>后续步骤

启动访问评审后，Azure AD 会自动向评审者发送一封电子邮件，提示他们评审访问权限。 如果用户未收到电子邮件，可以向他们发送[如何评审访问权限](active-directory-azure-ad-controls-perform-access-review.md)中的说明。  

访问评审期限结束后，或者管理员停止了访问评审后，请遵循[完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)中的步骤查看并应用结果。



