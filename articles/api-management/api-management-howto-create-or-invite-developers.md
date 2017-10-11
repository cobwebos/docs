---
title: "如何管理 Azure API 管理中的用户帐户 |Microsoft 文档"
description: "了解如何创建或邀请用户在 Azure API 管理"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 078abfa5-1e4f-4c9d-b9c7-a172bd19c1a2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: d3a50f6d22cbf1797f580078bc0d2cc9cefe5064
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>如何管理 Azure API 管理中的用户帐户
在 API 管理，开发人员是公开使用 API 管理的 Api 的用户。 本指南将演示如何创建并邀请开发人员使用 Api 和产品，你将提供给它们与 API 管理实例。 有关以编程方式管理用户帐户的信息，请参阅[用户实体](https://msdn.microsoft.com/library/azure/dn776330.aspx)中的文档[API 管理 REST](https://msdn.microsoft.com/library/azure/dn776326.aspx)引用。

## <a name="create-developer"></a>创建新的开发人员
若要创建新的开发人员，请单击**发布者门户**Azure 门户中的 API 管理系统服务。 将你转到 API 管理发布者门户。 如果尚未创建 API 管理服务实例，请参阅[创建 API 管理服务实例][ Create an API Management service instance]中[开始使用 Azure API 管理][ Get started with Azure API Management]教程。

![发布者门户][api-management-management-console]

单击**用户**从**API 管理**左侧的菜单，然后单击**将用户添加**。

![创建开发人员][api-management-create-developer]

输入**电子邮件**，**密码**，和**名称**新开发人员和单击**保存**。

![创建开发人员][api-management-add-new-user]

默认情况下，新创建的开发人员帐户均**Active**，并与关联**开发人员**组。

![新的开发人员][api-management-new-developer]

中的开发人员帐户**active**状态可以用于访问所有他们具有订阅的 Api。 若要将新创建的开发人员与其他组相关联，请参阅[如何将组与开发人员关联][How to associate groups with developers]。

## <a name="invite-developer"></a>邀请开发人员
若要邀请开发人员，请单击**用户**从**API 管理**左侧的菜单，然后单击**邀请用户**。

![邀请开发人员][api-management-invite-developer]

输入开发人员的姓名和电子邮件地址，然后单击**邀请**。

![邀请开发人员][api-management-invite-developer-window]

将显示一条确认消息，但新受邀请的开发人员在他们接受邀请后未出现在列表中，直到。 

![邀请确认][api-management-invite-developer-confirmation]

在开发人员邀请时，会将一封电子邮件发送到开发人员。 此电子邮件使用模板生成的并且可自定义。 有关详细信息，请参阅[配置电子邮件模板][Configure email templates]。

一旦接受邀请，该帐户就变为活动。

## <a name="block-developer"></a>停用或重新激活开发人员帐户
默认情况下，新创建的或受邀请的开发人员帐户均**Active**。 若要停用开发人员帐户，请单击**块**。 若要重新激活阻止的开发人员帐户，请单击**激活**。 阻止的开发人员帐户可以访问开发人员门户或调用任何 Api。 若要删除用户帐户，请单击**删除**。

![阻止开发人员][api-management-new-developer]

## <a name="reset-a-user-password"></a>重置用户密码
若要重置用户帐户的密码，请单击帐户的名称。

![重置密码][api-management-view-developer]

单击**重置密码**以发送到密码重置的用户的链接。

![重置密码][api-management-reset-password]

若要以编程方式使用用户帐户，请参阅[用户实体](https://msdn.microsoft.com/library/azure/dn776330.aspx)中的文档[API 管理 REST](https://msdn.microsoft.com/library/azure/dn776326.aspx)引用。 若要为特定值重置用户帐户密码，你可以使用[更新的用户](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser)操作并指定所需的密码。

## <a name="pending-verification"></a>待处理的验证
![待处理的验证][api-management-pending-verification]

## <a name="next-steps"></a>后续步骤
创建开发人员帐户后，可以将其与角色关联，并订阅产品和 Api。 有关详细信息，请参阅[如何创建和使用组][How to create and use groups]。

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
