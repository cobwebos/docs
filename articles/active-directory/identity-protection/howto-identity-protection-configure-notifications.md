---
title: “Azure Active Directory 标识保护”通知
description: 了解通知如何支持调查活动。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c786aaecd3ab2f18f242cea2f5c45838f9ecf3
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839341"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>“Azure Active Directory 标识保护”通知

Azure AD 标识保护会发送两种类型的自动生成的通知电子邮件，帮助你管理用户风险和风险检测：

- 检测到有风险的用户电子邮件
- 每周摘要电子邮件

本文概述了两种通知电子邮件。

## <a name="users-at-risk-detected-email"></a>检测到有风险的用户电子邮件

当“Azure AD 标识保护”检测到帐户受到威胁时，会生成“检测到有风险的用户”**** 的警报电子邮件。 该电子邮件包括指向[针对风险报告而标记的用户](./overview-identity-protection.md)**** 的链接。 建议立即调查有风险的用户。

此警报的配置允许你指定要生成警报的用户风险级别。 当用户的风险级别达到你指定的内容时，将生成电子邮件。 例如，如果将策略设置为对中等用户风险发出警报，并且用户 John 的用户风险评分由于实时登录风险而移动到中等风险，则会收到 "检测到风险的用户" 电子邮件。 如果用户的后续风险检测导致用户风险级别计算为指定的风险级别 (或更高的) ，则在重新计算用户风险评分时，你将收到风险检测到的其他用户。 例如，如果用户在1月1日移动到 "中等风险"，则如果设置设置为 "中等风险警报"，则将收到电子邮件通知。 如果该用户随后在1月5日发生了另一次风险检测，这也是 "中" 风险，并重新计算了用户风险评分并且仍为 "中"，则你将收到另一封电子邮件通知。 

但是，仅当发生风险检测 (导致用户风险级别发生更改的情况下，才会发送其他电子邮件通知) 比上次发送电子邮件的时间更晚。 例如，用户在1月1日上午5点登录，并且没有任何实时风险 (意味着由于登录) ，不会生成电子邮件。 十分钟后，在凌晨5:10，相同的用户再次登录并具有较高的实时风险，从而导致用户风险级别转为高，并发送一封电子邮件。 然后，在凌晨5:15，最初登录时间为5点的脱机风险评分因脱机风险处理而变化为高风险。 由于第一次登录之前首次登录的时间早于已触发电子邮件通知的第二个登录名，因此将不会发送其他标记为风险电子邮件的用户。

若要防止电子邮件超载，你只会在5秒的时间段内收到有风险检测到的电子邮件的用户。 这意味着，如果多个用户在同一5秒时间段内移动到指定的风险级别，我们将聚合并发送一封电子邮件，以表示所有这些用户的风险级别更改。

![检测到有风险的用户电子邮件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>配置检测到风险的用户警报

管理员可以设置：

- **触发生成此电子邮件的用户风险级别** - 默认情况下，此风险级别设置为“高”风险。
- **此电子邮件的收件人** -全局管理员、安全管理员或安全读者角色中的用户将自动添加到此列表中。 我们尝试向每个角色的前20个成员发送电子邮件。 如果用户已在 PIM 中注册以便按需提升为这些角色之一，则在 **发送电子邮件时，他们将只收到电子邮件**。
   - 或者，您可以在 **此处添加自定义电子邮件** ，用户必须具有适当的权限才能查看 Azure 门户中的链接报表。

在 " **Azure portal** **Azure Active Directory**  >  **安全**  >  **标识保护**用户" 下的 "  >  **检测到风险" 警报**下，在 Azure 门户中配置用户面临风险电子邮件。

## <a name="weekly-digest-email"></a>每周摘要电子邮件

每周摘要电子邮件中包含新风险检测的摘要。  
其中包括：

- 检测到新的有风险用户
- 实时检测到了新的风险登录 () 
- 指向“标识保护”中相关报告的链接

![每周摘要电子邮件](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

全局管理员、安全管理员或安全读者角色中的用户会自动添加到此列表中。 我们尝试向每个角色的前20个成员发送电子邮件。 如果用户已在 PIM 中注册以便按需提升为这些角色之一，则在 **发送电子邮件时，他们将只收到电子邮件**

### <a name="configure-weekly-digest-email"></a>配置每周摘要电子邮件

作为管理员，你可以打开或关闭每周摘要电子邮件，然后选择分配接收电子邮件的用户。

在 " **Azure portal** **Azure Active Directory**  >  **安全**  >  **标识保护**  >  **每周摘要**" 下的 Azure 门户中配置每周摘要电子邮件。

## <a name="see-also"></a>请参阅

- [Azure Active Directory 标识保护](./overview-identity-protection.md)
