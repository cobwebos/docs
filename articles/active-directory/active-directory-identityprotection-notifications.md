---
title: "Azure Active Directory Identity Protection 通知 | Microsoft 文档"
description: "了解通知如何支持你的调查活动。"
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
ms.date: 10/20/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: afb51a4513a7f1bcbbc85394d8dc7ab8af762f43


---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知
Azure AD Identity Protection 会发送两种类型的自动生成的通知电子邮件，帮助你管理用户风险和风险事件：

* 用户受威胁的警报电子邮件
* 每周摘要电子邮件

## <a name="user-compromised-alert-email"></a>用户受威胁的警报电子邮件
当 Azure AD Identity Protection 确定帐户受到威胁时，会生成用户受威胁的电子邮件警报。 该电子邮件包含指向 Identity Protection 仪表板中针对风险报告而标记的用户的链接。 我们建议你立即调查受威胁的通知。

## <a name="weekly-digest-email"></a>每周摘要电子邮件
每周摘要电子邮件中包含新风险事件的摘要。<br>
其中包括：

* 有风险的用户
* 可疑活动
* 检测到的漏洞
* 指向 Identity Protection 中相关报告的链接

<br>
![修正](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

可以关闭每周摘要电子邮件发送。
<br><br>
![用户风险](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>

**若要打开相关的配置对话框**：

1. 在“Azure AD Identity Protection”边栏选项卡中，单击“设置”。
   <br><br>
   ![用户风险策略](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
   <br>
2. 在“常规”部分中，单击“通知”。
   <br><br>
   ![用户风险策略](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
   <br>

## <a name="see-also"></a>另请参阅
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 




<!--HONumber=Nov16_HO3-->


