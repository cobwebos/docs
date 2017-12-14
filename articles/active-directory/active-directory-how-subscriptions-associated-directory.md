---
title: "如何将现有的 Azure 订阅添加到 Azure AD 目录 | Microsoft Docs"
description: "如何将现有的订阅添加到 Azure AD 目录"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e14d1c54a084a66bd33904b8c90e2973c0f7d6fb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>如何将 Azure 订阅添加到 Azure Active Directory
本文介绍 Azure 订阅与 Azure Active Directory (Azure AD) 之间的关系，以及如何向 Azure AD 目录添加现有的订阅。 Azure 订阅与 Azure AD 建立了信任关系，也即会委托该目录对用户、服务和设备进行身份验证。 多个订阅可以委托同一个目录，但每个订阅只能委托一个目录。 

订阅与目录之间的这种委托关系不同于订阅与 Azure 中其他资源（网站、数据库等）之间的委托关系。 如果某个订阅过期，则与该订阅关联的其他资源的访问权限也会终止。 但是，Azure AD 目录会保留在 Azure 中，你可以将其他订阅与该目录相关联，然后使用新订阅管理目录。

所有用户都只有一个用于验证其身份的主目录，但他们也可以是其他目录中的来宾。 在 Azure AD 中，可以看到用户帐户是其成员或来宾的目录。

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>将现有订阅添加到 Azure AD 目录
使用帐户登录时，该帐户必须存在于订阅所关联的当前目录中，同时存在于要向其添加订阅的目录中。 

1. 使用充当订阅（其所有权需转让）的“帐户管理员”的帐户登录到 [Azure 帐户中心](https://account.azure.com/Subscriptions)。
2. 确保需要让其成为订阅所有者的用户位于目标目录中。
3. 单击“转让订阅”。
4. 指定接收方。 接收方会自动收到含有接受链接的电子邮件。
5. 接收方单击该链接并遵照说明操作，包括输入他们的付款信息。 接收方成功后，订阅将转让。 
6. 订阅的默认目录会更改为该用户所在的目录。


## <a name="next-steps"></a>后续步骤
* 若要详细了解如何更改 Azure 订阅的管理员，请参阅[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)
* 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [Understanding resource access in Azure](active-directory-understanding-resource-access.md)（了解 Azure 中的资源访问）
* 有关如何在 Azure AD 中分配角色的详细信息，请参阅 [在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
