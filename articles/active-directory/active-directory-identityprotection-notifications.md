---
title: "Azure Active Directory Identity Protection 通知 | Microsoft Docs"
description: "了解通知如何支持调查活动。"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 079d16bbf75cd2b3b94269d684e1ae1a0e6aa967
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知
Azure AD Identity Protection 发送两种类型的自动的通知电子邮件来帮助你管理用户风险和风险事件：

* 用户受威胁的警报电子邮件
* 每周摘要电子邮件

## <a name="user-compromised-alert-email"></a>用户受威胁的警报电子邮件
当 Azure AD Identity Protection 确定帐户受到威胁时，会生成用户受威胁的电子邮件警报。 该电子邮件包含指向 Identity Protection 仪表板中针对风险报告而标记的用户的链接。 建议立即调查已泄漏帐户的通知。

## <a name="weekly-digest-email"></a>每周摘要电子邮件
每周摘要电子邮件中包含新风险事件的摘要。<br>
其中包括：

* 有风险的用户
* 可疑活动
* 检测到的漏洞
* 指向 Identity Protection 中相关报告的链接

<br>
![修正](./media/active-directory-identityprotection-notifications/400.png "修正")
<br>

可以关闭每周摘要电子邮件发送。
<br><br>
![用户风险](./media/active-directory-identityprotection-notifications/62.png "用户风险")
<br>

**若要打开相关的配置对话框**：

1. 在“Azure AD Identity Protection”边栏选项卡中，单击“设置”。
   <br><br>
   ![用户风险策略](./media/active-directory-identityprotection-notifications/401.png "用户风险策略")
   <br>
2. 在“常规”部分中，单击“通知”。
   <br><br>
   ![用户风险策略](./media/active-directory-identityprotection-notifications/405.png "用户风险策略")
   <br>

## <a name="see-also"></a>另请参阅
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
