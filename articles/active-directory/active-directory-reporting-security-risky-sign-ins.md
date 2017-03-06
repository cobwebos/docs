---
title: "Azure Active Directory 门户中的“有风险的登录”报表 - 预览 | Microsoft Docs"
description: "了解 Azure Active Directory 门户中的“有风险的登录”报表 - 预览"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 69b2166dcbc3e4abd99084b47907c90e157791de
ms.lasthandoff: 02/22/2017


---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal---preview"></a>Azure Active Directory 门户中的“有风险的登录”报表 - 预览

可以通过 Azure Active Directory [预览版](active-directory-preview-explainer.md)中的安全报告，了解你的环境中用户帐户泄露的可能性。 

Azure Active Directory 可以检测到与用户帐户相关的可疑操作。 每检测到一个可疑操作，就会创建一个名为“风险事件”的记录。 有关更多详细信息，请参阅 [Azure Active Directory 风险事件](active-directory-identity-protection-risk-events.md)。 

检测到的风险事件用于计算：

- **风险登录** - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 有关更多详细信息，请参阅[风险登录](active-directory-identityprotection.md#risky-sign-ins)。 

- **已标记为存在风险的用户** - 风险用户是指可能已泄露的用户帐户。 有关更多详细信息，请参阅[已标记为存在风险的用户](active-directory-identityprotection.md#users-flagged-for-risk)。  

在 Azure 门户中，可在“Azure Active Directory”边栏选项卡上的“安全”部分中找到安全报表。 

![有风险的登录](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory 免费版和基本版

Azure Active Directory 免费版和基本版提供为用户检测到的有风险的登录列表。 风险事件报告提供以下内容：

- **用户** - 在登录操作期间使用的用户的名称
- **IP** - 用于连接到 Azure Active Directory 的设备的 IP 地址
- **位置** - 用于连接到 Azure Active Directory 的位置
- **登录时间** - 进行登录的时间
- **状态** - 登录的状态

此报告提供了下载报告数据的选项。

![有风险的登录](./media/active-directory-reporting-security-risky-sign-ins/01.png)

可以根据自己对风险登录的调查情况，以下述操作的形式向 Azure Active Directory 提供反馈：

- 解决
- 标记为误报
- 忽略
- 重新激活

![有风险的登录](./media/active-directory-reporting-security-risky-sign-ins/21.png)

有关更多详细信息，请参阅[手动关闭风险事件](active-directory-identityprotection.md#closing-risk-events-manually)。

## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium 版

Azure Active Directory Premium 版中有风险的登录报表提供：

- 有关已检测到的[风险事件类型](active-directory-identity-protection-risk-events.md)的聚合信息

- 一个用于下载报表的选项


![有风险的登录](./media/active-directory-reporting-security-risky-sign-ins/456.png)


选择风险事件时，可获得此风险事件的详细报表视图，使你能够：

- 一个用于配置[用户风险补救策略](active-directory-identityprotection.md#user-risk-security-policy)的选项  

- 查看风险事件的检测时间线  

- 查看已为其检测到此风险事件的用户的列表

- [手动关闭风险事件](active-directory-identityprotection.md#closing-risk-events-manually)或重新激活已手动关闭的风险事件。 


![有风险的登录](./media/active-directory-reporting-security-risky-sign-ins/457.png)

选择用户时，可获取此用户的详细报表视图，使你能够：

- 打开“所有的登录”视图

- 重置用户密码

- 清除所有事件

- 为用户调查报告的风险事件。 


![有风险的登录](./media/active-directory-reporting-security-risky-sign-ins/324.png)


若要调查风险事件，请从列表选择某个事件。  
这将打开此风险事件的“详细信息”边栏选项卡。 在“详细信息”边栏选项卡上，可以选择[手动关闭风险事件](active-directory-identityprotection.md#closing-risk-events-manually)或重新激活已手动关闭的风险事件。 


![有风险的登录](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>后续步骤

- 有关 Azure Active Directory Identity Protection 的详细信息，请参阅 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)。


