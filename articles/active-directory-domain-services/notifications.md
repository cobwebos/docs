---
title: Azure AD 域服务的电子邮件通知 | Microsoft Docs
description: 了解如何配置电子邮件通知，以提醒你 Azure Active Directory 域服务托管域中的问题
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: c273b1a6c497ec8dcb8c5bc5f50ccfccc9fce155
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968011"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>为 Azure Active Directory 域服务中的问题配置电子邮件通知

Azure 平台会监视 Azure Active Directory 域服务 (Azure AD DS) 托管域的运行状况。 Azure 门户中的运行状况页显示托管域的任何警报。 为了确保及时对问题做出响应，可以将电子邮件通知配置为在 Azure AD DS 托管域中检测到运行状况警报时立即报告。

本文介绍如何为托管域配置电子邮件通知收件人。

## <a name="email-notification-overview"></a>电子邮件通知概述

若要提醒你有关托管域的问题，可以配置电子邮件通知。 这些电子邮件通知指定存在警报的托管域，并提供检测时间以及指向 Azure 门户中运行状况页的链接。 然后，你可以按照所提供的故障排除建议解决这些问题。

以下示例电子邮件通知指示托管域上生成了严重警告或警报：

![电子邮件通知示例](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> 单击邮件中的链接之前，请始终确保电子邮件来自已验证的 Microsoft 发件人。 电子邮件通知始终来自 `azure-noreply@microsoft.com` 地址。

### <a name="why-would-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

Azure AD DS 发送有关托管域的重要更新的电子邮件通知。 这些通知仅适用于影响服务并应立即解决的紧急问题。 在托管域上，警报会触发每个电子邮件通知。 这些警报也会出现在 Azure 门户中，可以在 [Azure AD DS 运行状况页][check-health]中进行查看。

Azure AD DS 不会发送用于广告、更新或销售目的的电子邮件。

### <a name="when-will-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

当托管域中出现[新警报][troubleshoot-alerts]时，会立即发送通知。 如果警报未解决，每隔四天将另外发送一次电子邮件通知进行提醒。

### <a name="who-should-receive-the-email-notifications"></a>谁会收到电子邮件通知？

Azure AD DS 的电子邮件收件人列表应由能够管理和更改托管域的人员组成。 应将此电子邮件列表视为任何警报和问题的“第一个响应方”。

最多可另外添加 5 个电子邮件通知收件人。 如果你希望 5 个以上的收件人接收电子邮件通知，请创建一个通讯组列表并改将其添加到通知列表。

还可以选择让 Azure AD 目录的所有全局管理员和“AAD DC 管理员”组的每个成员都接收电子邮件通知。 Azure AD DS 仅向最多 100 个电子邮件地址发送通知，其中包括全局管理员和 AAD DC 管理员的列表。

## <a name="configure-email-notifications"></a>配置电子邮件通知

若要查看现有电子邮件通知收件人或添加其他收件人，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。
1. 选择你的托管域，例如“aaddscontoso.com”。
1. 在 Azure AD DS 资源窗口的左侧，选择“通知设置”。 此时会显示电子邮件通知的现有收件人。
1. 若要添加电子邮件收件人，请在其他收件人表中输入电子邮件地址。
1. 完成后，在顶部导航栏中选择“保存”。

> [!WARNING]
> 更改通知设置时，将更新整个托管域的通知设置，而不是只更新你自己的通知设置。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>我收到了有关警报的电子邮件通知，但在登录到 Azure 门户后没有任何警报。 发生了什么情况？

如果警报已解决，警报将从 Azure 门户中清除。 最可能的原因是收到电子邮件通知的其他人已解决了托管域上的警报，或 Azure 平台自动解决了此警报。

### <a name="why-can-i-not-edit-the-notification-settings"></a>为什么我无法编辑通知设置？

如果无法访问 Azure 门户中的通知设置页，则无权编辑托管域。 请与全局管理员联系，以获取编辑 Azure AD DS 资源的权限，否则系统会将你从收件人列表中删除。

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>我似乎没有收到电子邮件通知，即使已提供我的电子邮件地址也没有收到。 为什么？

请检查电子邮件中的垃圾邮件文件夹中是否存在通知，并确保允许发件人 `azure-noreply@microsoft.com` 发送通知。

## <a name="next-steps"></a>后续步骤

若要详细了解如何排查可能报告的某些问题，请参阅[解决托管域的警报][troubleshoot-alerts]。

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
