---
title: “Azure Active Directory 标识保护”通知 | Microsoft Docs
description: 了解通知如何支持调查活动。
services: active-directory
keywords: Azure Active Directory 标识保护, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0065ec03695ee977133ae2ec43aafba7d5bfff78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784344"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>“Azure Active Directory 标识保护”通知

Azure AD 标识保护会发送两种类型的自动生成的通知电子邮件，帮助你管理用户风险和风险事件：

- 检测到有风险的用户电子邮件
- 每周摘要电子邮件

本文概述了两种通知电子邮件。


## <a name="users-at-risk-detected-email"></a>检测到有风险的用户电子邮件

当“Azure AD 标识保护”检测到帐户受到威胁时，会生成“检测到有风险的用户”  的警报电子邮件。 该电子邮件包括指向[针对风险报告而标记的用户](../reports-monitoring/concept-user-at-risk.md)  的链接。 建议立即调查有风险的用户。

此警报的配置，可指定哪些用户风险级别在你想要生成的警报。 用户的风险级别达到已指定了什么; 时，将生成电子邮件但是，它们移动到此用户风险级别后，不会收到有关此用户的风险检测到电子邮件警报的新用户。 例如，如果设置要对中等用户风险和 John 将移到中等风险用户发出警报的策略，您将接收 John 检测到风险电子邮件的用户。 但是，如果 John 然后移到高风险或有其他风险事件，不会收到在检测到风险警报第二个用户。

![检测到有风险的用户电子邮件](./media/notifications/01.png)


### <a name="configuration"></a>配置

管理员可以设置：

- **触发生成此电子邮件的用户风险级别**-默认情况下，风险级别设置为"高"风险。
- **此邮件的收件人** - 收件人默认包括所有全局管理员。 全局管理员还可将其他全局管理员、安全管理员、安全读取者添加为收件人。  


要打开相关对话框，请单击“标识保护”页中“设置”部分的“警报”    。

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

要打开相关对话框，请单击“标识保护”页中“设置”部分的“每周摘要”    。

![检测到有风险的用户电子邮件](./media/notifications/04.png)


## <a name="see-also"></a>另请参阅

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
