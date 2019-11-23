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

Azure AD Identity Protection sends two types of automated notification emails to help you manage user risk and risk detections:

- 检测到有风险的用户电子邮件
- 每周摘要电子邮件

本文概述了两种通知电子邮件。

## <a name="users-at-risk-detected-email"></a>检测到有风险的用户电子邮件

当“Azure AD 标识保护”检测到帐户受到威胁时，会生成“检测到有风险的用户”的警报电子邮件。 该电子邮件包括指向[针对风险报告而标记的用户](../reports-monitoring/concept-user-at-risk.md)的链接。 建议立即调查有风险的用户。

The configuration for this alert allows you to specify at what user risk level you want the alert to be generated. The email will be generated when the user's risk level reaches what you have specified; however, you will not receive new users at risk detected email alerts for this user after they move to this user risk level. For example, if you set the policy to alert on medium user risk and your user John moves to medium risk, you will receive the users at risk detected email for John. However, you will not receive a second user at risk detected alert if John then moves to high risk or has additional risk detections.

![检测到有风险的用户电子邮件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure users at risk detected alerts

管理员可以设置：

- **The user risk level that triggers the generation of this email** - By default, the risk level is set to “High” risk.
- **此邮件的收件人** - 收件人默认包括所有全局管理员。 全局管理员还可将其他全局管理员、安全管理员、安全读取者添加为收件人。
   - Optionally you can **Add additional emails to receive alert notifications** this feature is a preview and users defined must have the appropriate permissions to view the linked reports in the Azure portal.

Configure the users at risk email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Users at risk detected alerts**.

## <a name="weekly-digest-email"></a>每周摘要电子邮件

The weekly digest email contains a summary of new risk detections.  
其中包括：

- 有风险的用户
- 可疑活动
- 检测到的漏洞
- 指向“标识保护”中相关报告的链接

![每周摘要电子邮件](./media/howto-identity-protection-configure-notifications/400.png)

By default, recipients include all Global Admins. 全局管理员还可将其他全局管理员、安全管理员、安全读取者添加为收件人。

### <a name="configure-weekly-digest-email"></a>Configure weekly digest email

As an administrator, you can switch sending a weekly digest email on or off and choose the users assigned to receive the email.

Configure the weekly digest email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>另请参阅

- [Azure Active Directory 标识保护](../active-directory-identityprotection.md)
