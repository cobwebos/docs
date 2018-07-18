---
title: Azure Active Directory Identity Protection 通知 | Microsoft Docs
description: 了解通知如何支持调查活动。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e72cfab8886f28982e5451362704d6a7a9fe59e4
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713807"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection 会发送两种类型的自动生成的通知电子邮件，帮助你管理用户风险和风险事件：

- 检测到有风险的用户电子邮件
- 每周摘要电子邮件

本文概述了两种通知电子邮件。


## <a name="users-at-risk-detected-email"></a>检测到有风险的用户电子邮件

当 Azure AD Identity Protection 检测到帐户受到威胁时，会生成“检测到有风险的用户”的警报电子邮件。 该电子邮件包括指向[针对风险报告而标记的用户](active-directory-reporting-security-user-at-risk.md)的链接。 建议立即调查有风险的用户。

![检测到有风险的用户电子邮件](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>配置

管理员可以设置：

- **触发生成此邮件的风险级别** - 该风险级别默认设置为“高”风险。
- **此邮件的收件人** - 收件人默认包括所有全局管理员。 全局管理员还可将其他全局管理员、安全管理员、安全读者添加为收件人。  


要打开相关对话框，请单击“Identity Protection”页中“设置”部分的“警报”。

![检测到有风险的用户电子邮件](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>每周摘要电子邮件

每周摘要电子邮件中包含新风险事件的摘要。  
其中包括：

- 有风险的用户

- 可疑活动

- 检测到的漏洞

- 指向 Identity Protection 中相关报告的链接

    ![补救](./media/active-directory-identityprotection-notifications/400.png "补救")

### <a name="configuration"></a>配置

管理员可以关闭发送每周摘要电子邮件。

![用户风险](./media/active-directory-identityprotection-notifications/62.png "用户风险")

要打开相关对话框，请单击“Identity Protection”页中“设置”部分的“每周摘要”。

![检测到有风险的用户电子邮件](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>另请参阅

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
