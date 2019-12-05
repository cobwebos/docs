---
title: “Azure Active Directory 标识保护”通知
description: 了解通知如何支持调查活动。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382158"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>“Azure Active Directory 标识保护”通知

Azure AD 标识保护会发送两种类型的自动生成的通知电子邮件，帮助你管理用户风险和风险检测：

- 检测到有风险的用户电子邮件
- 每周摘要电子邮件

本文概述了两种通知电子邮件。

## <a name="users-at-risk-detected-email"></a>检测到有风险的用户电子邮件

当“Azure AD 标识保护”检测到帐户受到威胁时，会生成“检测到有风险的用户”的警报电子邮件。 该电子邮件包括指向**针对风险报告而标记的用户[](../reports-monitoring/concept-user-at-risk.md)** 的链接。 建议立即调查有风险的用户。

此警报的配置允许你指定要生成警报的用户风险级别。 当用户的风险级别达到你指定的风险级别时，将生成电子邮件；但是，在用户达到此用户风险级别后，你将不会收到针对该用户的新的用户检测到风险电子邮件警报。 例如，如果你将策略设置为对中等用户风险发出警报，并且你的用户 John 达到中等风险，那么你将收到针对 John 的用户检测到风险电子邮件。 但是，如果 John 发生风险，则不会收到第二个用户检测到风险的警报。

![检测到有风险的用户电子邮件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>配置检测到风险的用户警报

管理员可以设置：

- **触发生成此电子邮件的用户风险级别** - 默认情况下，此风险级别设置为“高”风险。
- **此邮件的收件人** - 收件人默认包括所有全局管理员。 全局管理员还可将其他全局管理员、安全管理员、安全读取者添加为收件人。
   - 另外，还可以**添加其他电子邮件来接收警报通知**此功能是预览，而定义的用户必须具有相应的权限才能查看 Azure 门户中的链接报表。

在**Azure Active Directory** > **安全** > **Identity Protection** > **用户处于风险检测到警报**的情况下，在**Azure 门户**中为用户配置风险电子邮件。

## <a name="weekly-digest-email"></a>每周摘要电子邮件

每周摘要电子邮件中包含新风险检测的摘要。  
其中包括：

- 有风险的用户
- 可疑活动
- 检测到的漏洞
- 指向“标识保护”中相关报告的链接

![每周摘要电子邮件](./media/howto-identity-protection-configure-notifications/400.png)

默认情况下，收件人包括所有全局管理员。 全局管理员还可将其他全局管理员、安全管理员、安全读取者添加为收件人。

### <a name="configure-weekly-digest-email"></a>配置每周摘要电子邮件

作为管理员，你可以打开或关闭每周摘要电子邮件，然后选择分配接收电子邮件的用户。

在**Azure 门户**中配置每周摘要电子邮件**Azure Active Directory** > **安全** > **Identity Protection** > **每周摘要**"。

## <a name="see-also"></a>请参阅

- [Azure Active Directory 标识保护](../active-directory-identityprotection.md)
