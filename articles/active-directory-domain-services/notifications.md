---
title: Azure AD 域服务的电子邮件通知 |微软文档
description: 了解如何配置电子邮件通知以提醒您 Azure 活动目录域服务托管域中的问题
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: ff5b0b430de53a67f4de0dacbd76a38a7de6e284
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475806"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>为 Azure 活动目录域服务中的问题配置电子邮件通知

Azure 活动目录域服务 （Azure AD DS） 托管域的运行状况由 Azure 平台监视。 Azure 门户中的运行状况状态页显示托管域的任何警报。 为了确保及时响应问题，可以将电子邮件通知配置为在 Azure AD DS 托管域中检测到运行状况警报后立即报告。

本文介绍如何为 Azure AD DS 托管域配置电子邮件通知收件人。

## <a name="email-notification-overview"></a>电子邮件通知概述

要提醒您 Azure AD DS 托管域的问题，可以配置电子邮件通知。 这些电子邮件通知指定警报存在的 Azure AD DS 托管域，以及提供检测时间和指向 Azure 门户中运行状况页的链接。 然后，您可以按照提供的故障排除建议来解决问题。

以下示例电子邮件通知指示在 Azure AD DS 托管域上生成了严重警告或警报：

![电子邮件通知示例](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> 在单击邮件中的链接之前，请务必确保电子邮件来自经过验证的 Microsoft 发件人。 电子邮件通知始终来自地址`azure-noreply@microsoft.com`。

### <a name="why-would-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

Azure AD DS 会发送有关托管域的重要更新的电子邮件通知。 这些通知仅适用于影响服务的紧急问题，应立即解决。 每个电子邮件通知都由 Azure AD DS 托管域上的警报触发。 警报也显示在 Azure 门户中，可以在 Azure AD [DS 运行状况页上][check-health]查看。

Azure AD DS 不会为广告、更新或销售目的发送电子邮件。

### <a name="when-will-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

在 Azure AD DS 托管域上发现[新警报][troubleshoot-alerts]时，将立即发送通知。 如果警报未解决，则每四天发送一次其他电子邮件通知作为提醒。

### <a name="who-should-receive-the-email-notifications"></a>谁会收到电子邮件通知？

Azure AD DS 的电子邮件收件人列表应由能够管理和更改托管域的人员组成。 此电子邮件列表应视为任何警报和问题的"第一响应者"。

您最多可以添加五个其他电子邮件收件人的电子邮件通知。 如果希望超过五个收件人用于电子邮件通知，请创建一个通讯组列表，并将其添加到通知列表中。

您还可以选择让 Azure AD 目录的所有*全局管理员*和*AAD DC 管理员*组的每个成员接收电子邮件通知。 Azure AD DS 仅向多达 100 个电子邮件地址发送通知，包括全局管理员和 AAD DC 管理员的列表。

## <a name="configure-email-notifications"></a>配置电子邮件通知

要查看现有电子邮件通知收件人或添加其他收件人，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**。
1. 选择 Azure AD DS 托管域，如*aaddscontoso.com*。
1. 在 Azure AD DS 资源窗口的左侧，选择**通知设置**。 将显示电子邮件通知的现有收件人。
1. 要添加电子邮件收件人，请在其他收件人表中输入电子邮件地址。
1. 完成后，选择"**在**顶部导航上保存"。

> [!WARNING]
> 更改通知设置时，将更新整个 Azure AD DS 托管域的通知设置，而不仅仅是更新您自己。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>我收到了有关警报的电子邮件通知，但在登录到 Azure 门户后没有任何警报。 发生了什么情况？

如果警报已解决，则从 Azure 门户清除警报。 最可能的原因是，收到电子邮件通知的其他人解决了 Azure AD DS 托管域上的警报，或者该警报被 Azure 平台自动解析。

### <a name="why-can-i-not-edit-the-notification-settings"></a>为什么我无法编辑通知设置？

如果无法访问 Azure 门户中的通知设置页，则无权编辑 Azure AD DS 托管域。 您必须与全局管理员联系，以获得编辑 Azure AD DS 资源的权限或从收件人列表中删除的权限。

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>我似乎没有收到电子邮件通知，即使已提供我的电子邮件地址也没有收到。 为什么？

检查电子邮件中的垃圾邮件或垃圾邮件文件夹，了解通知，并确保允许 发件人`azure-noreply@microsoft.com`。

## <a name="next-steps"></a>后续步骤

有关排除可能报告的一些问题的疑难解答的详细信息，请参阅[解决 Azure AD DS 托管域上的警报][troubleshoot-alerts]。

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
