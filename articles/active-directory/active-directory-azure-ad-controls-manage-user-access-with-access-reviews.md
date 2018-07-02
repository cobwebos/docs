---
title: 使用 Azure AD 访问评审管理用户访问权限 | Microsoft Docs
description: 了解如何使用 Azure Active Directory 访问评审管理（以组成员身份）用户访问权限或对应用程序的分配
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.openlocfilehash: 86ca14c8865ee82d61cb63a20f66c508f532f124
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019996"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>使用 Azure AD 访问评审管理用户访问权限

使用 Azure Active Directory (Azure AD) 可以轻松确保用户有适当的访问权限。 为此，可让用户本人或决策人参与访问评审，鉴定（或“证明”）用户的访问权限。 审阅者可基于 Azure AD 的建议，针对每个用户就继续访问的需求提供意见。 访问评审完成后，即可进行更改，并删除不再需要访问权限的用户的访问权限。

> [!NOTE]
> 如果仅评审来宾用户的访问权限，而无需评审所有用户的访问权限，请参阅[使用访问评审管理来宾用户访问权限](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)。 如果希望评审具有管理角色（如全局管理员）的用户成员身份，请参阅[在 Azure AD Privileged Identity Management 中启动访问评审](active-directory-privileged-identity-management-how-to-start-security-review.md)。 
>
>

## <a name="prerequisites"></a>先决条件 


访问评审在 Azure AD 的 Premium P2 版本中提供，后者随附在 Microsoft 企业移动性 + 安全性的 E5 中。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。 与此功能交互的每个用户，无论是创建评审、填写评审还是确认评审，都需要许可证。 

## <a name="create-and-perform-an-access-review"></a>创建和执行访问评审

访问评审中可有一个或多个用户作为审阅者。  

1. 在具有一个或多个成员的 Azure AD 中选择一个组。 或者选择连接到 Azure AD（已为其分配一个或多个用户）的应用程序。 

2. 决定是由每个用户评审自己的访问权限，还是由一个或多个用户评审每个人的访问权限。

3. 使访问评审能够显示在审阅者的访问面板上。 以全局管理员或用户帐户管理员身份，转到[访问评审页面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

4. 开始访问评审。 有关详细信息，请参阅[创建访问评审](active-directory-azure-ad-controls-create-access-review.md)。

5. 要求审阅者提供输入。 默认情况下，他们会收到来自 Azure AD 的电子邮件（其中包含访问面板的链接），他们将在该访问面板中[执行访问评审](active-directory-azure-ad-controls-perform-access-review.md)。

6. 如果审阅者尚未提供输入，可以要求 Azure AD 向他们发送提醒。 默认情况下，Azure AD 自动在中途向还未作出回复的审阅者发送结束日期提醒。

7. 审阅者提供输入后，将停止访问评审并应用更改。 有关详细信息，请参阅[完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)。


## <a name="next-steps"></a>后续步骤

[创建对组成员的访问评审或对应用程序的访问](active-directory-azure-ad-controls-create-access-review.md)




