---
title: “Azure Active Directory 标识保护”通知 | Microsoft Docs
description: 了解通知如何支持调查活动。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dcc7d2d03b3c4a3f4c8c772f38aa3785080d986
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335363"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>“Azure Active Directory 标识保护”通知

Azure AD 标识保护会发送两种类型的自动生成的通知电子邮件，帮助你管理用户风险和风险事件：

- 检测到有风险的用户电子邮件
- 每周摘要电子邮件

本文概述了两种通知电子邮件。

## <a name="users-at-risk-detected-email"></a>检测到有风险的用户电子邮件

当“Azure AD 标识保护”检测到帐户受到威胁时，会生成“检测到有风险的用户”的警报电子邮件。 该电子邮件包括指向[针对风险报告而标记的用户](../reports-monitoring/concept-user-at-risk.md)的链接。 建议立即调查有风险的用户。

此警报的配置允许你指定要为其生成警报的用户风险级别。 当用户的风险级别达到您指定的值时, 将生成该电子邮件;但是, 在此用户移动到此用户风险级别后, 你将不会收到风险检测到的新用户的电子邮件警报。 例如, 如果将策略设置为对中等用户风险发出警报, 并且用户 John 移动到 "中等风险", 则你将收到 John 收到电子邮件的 "有风险的用户"。 但是, 如果 John 再转向高风险或有其他风险事件, 则不会收到第二个处于风险检测到警报的用户。

![检测到有风险的用户电子邮件](./media/notifications/01.png)

### <a name="configuration"></a>配置

管理员可以设置：

- **触发此电子邮件生成的用户风险级别**-默认情况下, 风险级别设置为 "高" 风险。
- **此邮件的收件人** - 收件人默认包括所有全局管理员。 全局管理员还可将其他全局管理员、安全管理员、安全读取者添加为收件人。  

要打开相关对话框，请单击“标识保护”页中“设置”部分的“警报”。

![检测到有风险的用户电子邮件](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>每周摘要电子邮件

每周摘要电子邮件中包含新风险事件的摘要。  
其中包括：

- 有风险的用户
- 可疑活动
- 检测到的漏洞
- 指向“标识保护”中相关报告的链接

    ![补救](./media/notifications/400.png "补救")

### <a name="configuration"></a>配置

管理员可以关闭发送每周摘要电子邮件。

![用户风险](./media/notifications/62.png "用户风险")

要打开相关对话框，请单击“标识保护”页中“设置”部分的“每周摘要”。

![检测到有风险的用户电子邮件](./media/notifications/04.png)

## <a name="see-also"></a>请参阅

- [Azure Active Directory 标识保护](../active-directory-identityprotection.md)
