---
title: Azure AD 域服务的电子邮件通知 |Microsoft Docs "
description: 了解如何配置电子邮件通知，以提醒你有关 Azure Active Directory 域服务托管域中的问题
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 140edb3162d4f76c741a5692faa19325581a3ba7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704492"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>为 Azure Active Directory 域服务中的问题配置电子邮件通知

Azure 平台监视 Azure Active Directory 域服务（Azure AD DS）托管域的运行状况。 Azure 门户中的 "运行状况状态" 页显示托管域的任何警报。 为了确保及时对问题做出响应，可以将电子邮件通知配置为在 Azure AD DS 托管域中检测到警报时立即报告运行状况警报。

本文介绍如何为 Azure AD DS 托管域配置电子邮件通知收件人。

## <a name="email-notification-overview"></a>电子邮件通知概述

若要向你发出 Azure AD DS 托管域的问题警报，你可以配置电子邮件通知。 这些电子邮件通知指定存在警报的 Azure AD DS 托管域，并提供检测时间和 Azure 门户中的 "运行状况" 页的链接。 然后，你可以按照提供的疑难解答建议解决这些问题。

以下示例电子邮件通知指示在 Azure AD DS 托管域上生成了严重警告或警报：

![电子邮件通知示例](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> 在单击消息中的链接之前，请始终确保电子邮件来自已验证的 Microsoft 发送方。 电子邮件通知始终来自 `azure-noreply@microsoft.com` 地址。

### <a name="why-would-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

Azure AD DS 发送电子邮件通知，以了解有关托管域的重要更新。 这些通知仅适用于影响服务并应立即解决的紧急问题。 每个电子邮件通知都由 Azure AD DS 托管域上的警报触发。 这些警报也会显示在 Azure 门户中，并可在 " [AZURE AD DS 运行状况" 页][check-health]上查看。

Azure AD DS 不会发送电子邮件用于广告、更新或销售目的。

### <a name="when-will-i-receive-email-notifications"></a>为什么我会收到电子邮件通知？

当在 Azure AD DS 托管域上发现[新警报][troubleshoot-alerts]时，将立即发送通知。 如果未解决警报，则会将其他电子邮件通知作为提醒每四天发送一次。

### <a name="who-should-receive-the-email-notifications"></a>谁会收到电子邮件通知？

Azure AD DS 的电子邮件收件人列表应由能够管理和更改托管域的人员组成。 应将此电子邮件列表视为任何警报和问题的 "第一个响应者"。

最多可添加五个附加电子邮件收件人以获取电子邮件通知。 如果你希望5个以上的收件人发送电子邮件通知，请创建一个通讯组列表并改为将其添加到通知列表。

你还可以选择让 Azure AD 目录的所有*全局管理员*和*AAD DC 管理员*组的每个成员都收到电子邮件通知。 Azure AD DS 仅向最多100个电子邮件地址发送通知，包括全局管理员和 AAD DC 管理员的列表。

## <a name="configure-email-notifications"></a>配置电子邮件通知

若要查看现有电子邮件通知收件人或添加其他收件人，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**"。
1. 选择 Azure AD DS 托管域，如*aadds.contoso.com*。
1. 在 Azure AD DS 资源 "窗口的左侧，选择"**通知设置**"。 将显示电子邮件通知的现有收件人。
1. 若要添加电子邮件收件人，请在 "其他收件人" 表中输入电子邮件地址。
1. 完成后，在顶部导航栏中选择 "**保存**"。

> [!WARNING]
> 更改通知设置时，将更新整个 Azure AD DS 托管域的通知设置，而不是只更新自己的设置。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>我收到了有关警报的电子邮件通知，但在登录到 Azure 门户后没有任何警报。 发生了什么情况？

如果警报已解决，则会从 Azure 门户中清除警报。 最可能的原因是，接收电子邮件通知的其他人已在 Azure AD DS 托管域上解决了警报，或是 Azure 平台自动解决了该警报。

### <a name="why-can-i-not-edit-the-notification-settings"></a>为什么我无法编辑通知设置？

如果您无法访问 "Azure 门户中的" 通知设置 "页，则您没有编辑 Azure AD DS 托管域的权限。 必须与全局管理员联系，才能获得编辑 Azure AD DS 资源或从收件人列表中删除的权限。

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>我似乎没有收到电子邮件通知，即使已提供我的电子邮件地址也没有收到。 为什么？

检查电子邮件中的 "垃圾邮件" 或 "垃圾邮件" 文件夹中是否有通知，并确保允许 `azure-noreply@microsoft.com`的发送方。

## <a name="next-steps"></a>后续步骤

有关对可能报告的某些问题进行故障排除的详细信息，请参阅[解决 AZURE AD DS 托管域上的警报][troubleshoot-alerts]。

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
