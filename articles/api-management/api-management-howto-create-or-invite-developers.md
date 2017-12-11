---
title: "如何在 Azure API 管理中管理用户帐户 | Microsoft Docs"
description: "了解如何在 Azure API 管理中创建或邀请用户"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 6f2fd5e4c1a51fe9d1652c9970bcd8d76b25ab60
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>如何在 Azure API 管理中管理用户帐户
在 API 管理（预览版）中，开发人员是公开使用 API 管理的 API 的用户。 本指南演示如何进行创建并邀请开发人员使用 API 和产品，向 API 管理实例提供。 有关以编程方式管理用户帐户的信息，请参阅 [API 管理 REST](https://msdn.microsoft.com/library/azure/dn776326.aspx)参考中的[用户实体](https://msdn.microsoft.com/library/azure/dn776330.aspx)文档。

## <a name="create-developer"> </a>创建新开发人员
若要创建新开发人员，请单击 API 管理服务的 Azure 门户中的“发布者门户”。 这会转到 API 管理发布者门户。 如果尚未创建 API 管理服务实例，请参阅[创建 API 管理服务实例][Create an API Management service instance]。

![发布者门户][api-management-management-console]

单击左侧“API 管理”菜单中的“用户”，并单击“添加用户”。

![创建开发人员][api-management-create-developer]

为新开发人员输入“电子邮件”、“密码”和“名称”，并单击“保存”。

![创建开发人员][api-management-add-new-user]

默认情况下，新创建的开发人员帐户处于“活动”状态，并且与“开发人员”组相关联。

![新的开发人员][api-management-new-developer]

处于“活动”状态的开发人员帐户可用于访问它们具有订阅的所有 API。 要将新创建的开发人员与其他组相关联，请参阅[如何将组与开发人员关联][How to associate groups with developers]。

## <a name="invite-developer"> </a>邀请开发人员
要邀请开发人员，请单击左侧“API 管理“菜单中的“用户”，并单击“邀请用户”。

![邀请开发人员][api-management-invite-developer]

输入开发人员的名称和电子邮件地址，并单击“邀请”。

![邀请开发人员][api-management-invite-developer-window]

将显示一条确认消息，但新受邀请的开发人员之后不会出现在列表中，直到他们接受邀请。 

![邀请确认][api-management-invite-developer-confirmation]

当邀请一名开发人员时，会将一封电子邮件发送给开发人员。 此电子邮件使用一个模板生成并可自定义。 有关详细信息，请参阅[配置电子邮件模板][Configure email templates]。

一旦接受邀请，该帐户将变为活动状态。

## <a name="block-developer"> </a> 停用或重新激活开发人员帐户
默认情况下，新创建的或受邀请的开发人员帐户处于“活动”状态。 若要停用开发人员帐户，请单击“阻止”。 若要重新激活阻止的开发人员帐户，请单击“激活”。 阻止的开发人员帐户不能访问开发人员门户或调用任何 API。 若要删除用户帐户，请单击“删除”。

![阻止开发人员][api-management-new-developer]

## <a name="reset-a-user-password"></a>重置用户密码
若要重置用户帐户的密码，请单击该帐户的名称。

![重置密码][api-management-view-developer]

单击“重置密码”向用户发送链接以重置其密码。

![重置密码][api-management-reset-password]

若要以编程方式处理用户帐户，请参阅 [API 管理 REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) 参考中的[用户实体](https://msdn.microsoft.com/library/azure/dn776330.aspx)文档。 要将用户帐户密码重置为特定值，可使用[更新用户](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser)操作并指定所需密码。

## <a name="pending-verification"></a>待处理的验证
![待处理的验证][api-management-pending-verification]

## <a name="next-steps"> </a>后续步骤
创建开发人员帐户后，可以将其与角色相关联，并订阅产品和 API。 有关详细信息，请参阅[如何创建和使用组][How to create and use groups]。

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

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
