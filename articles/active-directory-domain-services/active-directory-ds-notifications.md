---
title: Azure Active Directory 域服务：通知设置 | Microsoft Docs
description: Azure AD 域服务通知设置
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: c6d827629850de88940f41febeeca61b812d692e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958519"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Azure AD 域服务中的通知设置

通过 Azure AD 域服务通知，在托管域上检测到运行状况警报时，会立即进行更新。  

此功能仅适用于不处于经典虚拟网络的托管域。


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>如何检查 Azure AD 域服务电子邮件通知设置

1. 导航到 Azure 门户上的 [Azure AD 域服务页面](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)
2. 从表中选择托管域
3. 在左侧导航窗格中，选择“通知设置”

此页列出了 Azure AD 域服务电子邮件通知的所有电子邮件收件人。

## <a name="what-does-an-email-notification-look-like"></a>电子邮件通知的外观如何？

下图是电子邮件通知的示例：

![电子邮件通知示例](./media/active-directory-domain-services-alerts/email-alert.png)

电子邮件指明存在警报的托管域，并提供检测时间以及指向 Azure 门户中 Azure AD 域服务运行状况页的链接。

> [!WARNING]
> 单击电子邮件中的链接之前，请始终确保电子邮件来自已验证的 Microsoft 发件人。 电子邮件始终来自电子邮件 azure-noreply@microsoft.com
>


## <a name="why-would-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

Azure AD 域服务发送有关重要域更新的电子邮件通知。  这些通知仅适用于紧急情况，它会影响你的服务，应立即处理。 在托管域上，警报会触发每个电子邮件通知。 这些警报也会出现在 Azure 门户中，可以在 [Azure AD 域服务运行状况页](active-directory-ds-check-health.md)进行查看。

Azure AD 域服务不向此列表发送用于广告、更新或销售目的电子邮件。

## <a name="when-will-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

当托管域中出现[新警报](active-directory-ds-troubleshoot-alerts.md)时，将立即发送通知。 如果未解决警报，每隔四天将发送一次电子邮件通知，以作提醒。

## <a name="who-should-receive-the-email-notifications"></a>谁会收到电子邮件通知？


 我们建议 Azure AD 域服务的电子邮件收件人列表包含能够管理和更改托管域的人员。 此电子邮件列表应视为发现任何问题时的“第一个响应方”。 如果你想要添加五个以上其他电子邮件，我们建议创建通讯组，而不是添加到通知列表。

你将能够添加最多五个其他电子邮件，以向其发送有关 Azure AD 域服务的通知。 此外，你还可以选择目录的所有全局管理员和“AAD DC 管理员”组的每个成员接收 Azure AD 域服务电子邮件通知。 Azure AD 域服务将仅向最多 100 个电子邮件地址发送通知，其中包括全局管理员和 AAD DC 管理员列表。


## <a name="how-to-add-an-additional-email-recipient"></a>如何添加其他电子邮件收件人

> [!WARNING]
> 更改通知设置时，将更改整个托管域的通知设置，而不仅仅是自己。

1. 导航到 Azure 门户上的 [Azure AD 域服务页面](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)。
2. 单击托管域。
3. 在左侧导航窗格中，单击“通知设置”。
4. 若要添加电子邮件，请在其他收件人表中键入电子邮件地址。
5. 在顶部导航栏中单击“保存”。

## <a name="frequently-asked-questions"></a>常见问题

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>我收到了有关警报的电子邮件通知，但在登录到 Azure 门户后没有任何警报。 发生了什么情况？

如果警报已解决，警报将从 Azure 门户中消失。 最可能的原因是收到电子邮件通知的其他人已解决了托管域上的警报，或 Azure AD 域服务自动解决了此警报。


#### <a name="why-can-i-not-edit-the-notification-settings"></a>为什么我无法编辑通知设置？

如果无法访问 Azure 门户中的通知设置页，则无权编辑 Azure AD 域服务。 你必须联系全局管理员以获取编辑 Azure AD 域服务资源的权限，或已从收件人列表中删除。

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>我似乎没有收到电子邮件通知，即使已提供我的电子邮件地址也没有收到。 为什么？

请在电子邮件垃圾邮件或垃圾文件夹中检查通知，确保你属于允许列表发件人 (azure-noreply@microsoft.com)。

## <a name="next-steps"></a>后续步骤
- [解决托管域中的警报](active-directory-ds-troubleshoot-alerts.md)
- [详细了解 Azure AD 域服务](active-directory-ds-overview.md)
- [联系产品团队](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
