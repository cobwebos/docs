---
title: "使用 Azure AD 访问评审管理来宾访问权限 | Microsoft Docs"
description: "使用 Azure Active Directory 访问评审管理属于组的成员或已分配到应用程序的来宾用户"
services: active-directory
documentationcenter: 
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
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>使用 Azure AD 访问评审管理来宾访问权限


使用 Azure Active Directory，可以借助 [Azure AD B2B 功能](active-directory-b2b-what-is-azure-ad-b2b.md)轻松实现跨组织边界的协作。 [管理员](active-directory-b2b-admin-add-users.md)或[最终用户](active-directory-b2b-how-it-works.md)可以邀请其他租户中的来宾用户。  这也适用于 Microsoft 帐户等社交标识。

可以轻松确保来宾用户拥有适当的访问权限。  为此，可让来宾本人或决策人参与访问评审，鉴定（或“证明”）来宾的访问权限。 审阅者可基于 Azure AD 的建议，针对每个用户就继续访问的需求提供意见。 访问评审完成后，即可进行更改，并删除不再需要访问权限的来宾的访问权限。

> [!NOTE]
> 本文档重点介绍如何评审来宾用户的访问权限。 如果想要评审所有用户（而不仅仅是来宾）的访问权限，请参阅[使用访问评审管理用户访问权限](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)指南。  如果希望评审具有管理角色（如全局管理员）的用户成员身份，请参阅[如何在 Azure AD PIM 中启动访问评审](active-directory-privileged-identity-management-how-to-start-security-review.md)。 
>
>

## <a name="prerequisites"></a>先决条件 

访问评审适用于 EMS E5 中包含的 Azure Active Directory Premium P2 版。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。  与此功能交互的每个用户，无论是将该功能用于创建评审、评审访问或应用评审，都需要许可证。  

如果要让来宾用户评审其自己的访问权限，请在 [Azure AD B2B 协作许可](active-directory-b2b-licensing.md)中详细了解来宾用户许可。

## <a name="creating-and-performing-an-access-review-for-guests"></a>创建并执行来宾的访问评审

首先，使访问评审显示在审阅者的访问面板上。  以全局管理员身份转到[访问评审页面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。 

Azure AD 支持采用多种方案评审来宾用户。  

选择以下方案之一：
 - Azure Active Directory 中包含一个或多个来宾成员的组 
 - 或者连接到 Azure Active Directory 的应用程序，该应用程序分配有一个或多个来宾用户，并确定是要让每个来宾自我评审其访问权限，还是让一个或多个用户评审每个来宾的访问权限。

 以下部分之一介绍了上述每种方案。

* [让来宾自我评审他们在组中的成员身份](#asking-guests-to-review-their-own-membership-in-a-group)
* [让发起人评审来宾在组中的成员身份](#asking-sponsors-to-review-guests-membership-in-a-group)
* [让来宾自我评审他们对应用程序的访问权限](#asking-guests-to-review-their-own-access-to-an-application)
* [让发起人评审来宾对应用程序的访问权限](#asking-sponsors-to-review-guests-access-to-an-application) 
* [让来宾评审访问权限的一般需求](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>让来宾自我评审他们在组中的成员身份

使用访问评审可以确保受邀并已添加到组中的用户需要持续提供访问权限。  一种方便的做法就是让来宾自己评审他们在该组中的成员身份。

1. 针对组启动访问评审，选择仅包括来宾用户成员的评审，然后，这些成员会自我评审。 请参阅[如何创建访问评审](active-directory-azure-ad-controls-create-access-review.md)，了解详细信息。
2. 让每个来宾自我评审其成员身份。  默认情况下，接受邀请的每个来宾会收到来自 Azure AD 的电子邮件，其中包含访问评审的链接。  Azure AD 为来宾提供了有关[如何评审其访问权限](active-directory-azure-ad-controls-perform-access-review.md)的说明。
3. 审阅者提供输入后，将停止访问评审并应用更改。 参阅[如何完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)，了解详细信息。 
4. 除了表明自己不需要持续访问权限的用户以外，还可以删除未做出答复的用户。  这是因为，未做出答复的用户可能不再接收电子邮件。
5. 如果未将组用于访问管理，还可以删除未选择参与评审的用户，因为他们未接受邀请。  这可能表示受邀用户的电子邮件地址拼写错误。  但是，如果将组用作通讯组列表，则可能未选择某些来宾用户参与，因为他们是联系人对象。

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>让发起人评审来宾在组中的成员身份

可以让发起人（例如组的所有者）评审来宾是否持续需要获得组中的成员身份。

1. 针对组启动访问评审，选择仅包括来宾用户成员的评审，并指定一个或多个评审者。 请参阅[如何创建访问评审](active-directory-azure-ad-controls-create-access-review.md)，了解详细信息。
2. 要求审阅者提供输入。 默认情况下，他们会收到来自 Azure AD 的电子邮件（其中包含访问面板的链接），他们将在该访问面板中[执行访问评审](active-directory-azure-ad-controls-perform-access-review.md)。
3. 审阅者提供输入后，将停止访问评审并应用更改。 参阅[如何完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)，了解详细信息。

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>让来宾自我评审他们对应用程序的访问权限

使用访问评审可以确保受邀参与特定应用程序的用户需要持续提供访问权限。  一种方便的做法就是让来宾自己评审他们的访问权限需求。

1. 针对应用程序启动访问评审，选择仅包括来宾的评审，然后，这些用户会评审自己的访问权限。 请参阅[如何创建访问评审](active-directory-azure-ad-controls-create-access-review.md)，了解详细信息。
2. 让每个来宾自我评审他们对应用程序的访问权限。  默认情况下，接受邀请的每个来宾会收到来自 Azure AD 的电子邮件，其中包含组织访问面板中访问评审的链接。  Azure AD 为来宾提供了有关[如何评审其访问权限](active-directory-azure-ad-controls-perform-access-review.md)的说明。
3. 审阅者提供输入后，将停止访问评审并应用更改。 参阅[如何完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)，了解详细信息。
4. 除了表明自己不需要持续访问权限的用户以外，还可以删除未做出答复的来宾用户，因为未做出答复的用户可能不再接收电子邮件。  还可以删除未选择参与评审的来宾用户，尤其是最近未邀请这些来宾时，因为这些用户未接受其邀请，所以无权访问应用程序。 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>让发起人评审来宾对应用程序的访问权限


可以让发起人（例如应用程序的所有者）评审来宾是否需要持续获得应用程序的访问权限。

1. 针对应用程序启动访问评审，选择仅包括来宾的评审，并指定一个或多个用户作为评审者。 请参阅[如何创建访问评审](active-directory-azure-ad-controls-create-access-review.md)，了解详细信息。
2. 要求审阅者提供输入。 默认情况下，他们会收到来自 Azure AD 的电子邮件（其中包含访问面板的链接），他们将在该访问面板中[执行访问评审](active-directory-azure-ad-controls-perform-access-review.md)。
3. 审阅者提供输入后，将停止访问评审并应用更改。 参阅[如何完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)，了解详细信息。

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>让来宾评审访问权限的一般需求

在某些组织中，来宾可能不知道其组成员身份。

> [!NOTE]
> 早期版本的 Azure 门户不允许 UserType 为 Guest 的用户进行管理访问，并且在某些情况下，目录中的管理员可能已使用 PowerShell 将来宾的 UserType 值更改为 Member。  如果目录中以前发生了这种情况，则上述查询可能不包括过去拥有管理访问权限的所有来宾用户，因此，需要更改来宾的 UserType，或手动将其包含在组成员身份中。

1. 如果尚不存在适当的组，请在 Azure AD 中创建一个安全组并在其中以成员的形式包含来宾。  例如，可以创建一个组并在其中包含手动维护的来宾成员身份。  或者，可以针对 Contoso 租户中 UserType 属性值为 Guest 的用户，以类似于“Guests of Contoso”的名称创建一个动态组。
2. 针对该组启动访问评审，将成员自己选择为评审者。 请参阅[如何创建访问评审](active-directory-azure-ad-controls-create-access-review.md)，了解详细信息。
3. 让每个来宾自我评审其成员身份。  默认情况下，接受邀请的每个来宾会收到来自 Azure AD 的电子邮件，其中包含组织访问面板中访问评审的链接。  Azure AD 为来宾提供了有关[如何评审其访问权限](active-directory-azure-ad-controls-perform-access-review.md)的说明。
4. 评审者提供输入后，请停止访问评审。 参阅[如何完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)，了解详细信息。
5. 删除已拒绝评审、未完成评审或者事先未接受邀请的来宾的来宾访问权限。   如果某些来宾是由于以前未接受邀请而选择参与评审的联系人，则可以使用 Azure 门户或 PowerShell 禁止其帐户登录。  如果来宾不再需要访问权限并且不是联系人，则可以使用 Azure 门户或 PowerShell 从目录中删除其用户对象。

## <a name="next-steps"></a>后续步骤

- [创建对组成员的访问评审或对应用程序的访问](active-directory-azure-ad-controls-create-access-review.md)







