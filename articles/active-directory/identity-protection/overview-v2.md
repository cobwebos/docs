---
title: Azure Active Directory Identity Protection（已刷新）是什么？ | Microsoft Docs
description: Azure Active Directory Identity Protection（已刷新）是什么？
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c3543b217339c39ad79c2125afdef8f087a70b3
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336680"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Azure Active Directory Identity Protection（已刷新）是什么？

Identity Protection 体验已经过刷新，以更好地保护组织的标识。 此刷新后的体验提供了：

- 经重新设计的管理体验，围绕与你最相关的风险 - 用户风险和登录风险

- 功能强大的调查体验，支持筛选、排序和智能下载

- 改进的用户风险计算，帮助你将工作重点放在最可能遭到入侵的用户上

- 新的 API 支持，可以编程方式访问风险数据

- 简化的管理员反馈流程，使你能够立即保护用户

- 新的监督式机器学习，提高风险评估的准确性



安全是组织当下关心的头等大事。 大多数安全漏洞出现在当攻击者通过窃取用户的标识来获取环境的访问权限时。 近年来，攻击者越来越擅于使用第三方违规，以及使用复杂的网络钓鱼攻击。 攻击者获取较低权限的用户帐户的访问权限后，即可通过横向移动，轻松访问重要的公司资源。 

为响应这些威胁，Azure AD Identity Protection 使你能够： 

- 主动防止滥用遭到入侵的标识 

- 检测到可疑活动时自动缓解风险 

- 调查有风险的用户和登录来解决潜在漏洞  

- 当用户风险达到指定阈值时发出警报 

 

Azure AD Identity Protection 是 Azure Active Directory Premium P2 的一项功能，它允许你配置策略，以便在用户标识遭到入侵或帐户所有者以外的其他人试图使用其标识登录时自动进行响应。 除了 Azure AD 提供的条件访问控制以外，这些策略还可以自动阻止或启用缓解措施，如密码重置或强制实施多重身份验证。 此外，Identity Protection 还提供监视和报告功能，以便更深入地了解组织中的风险和潜在威胁。 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]


## <a name="risk-events"></a>风险事件

Azure AD Identity Protection 检测到以下风险事件： 

 

| 风险事件类型 | 描述 | 检测类型 |
| ---             | ---         | ---            |
| 异常位置登录 | 从异常位置（基于用户最近的登录）进行登录。 | 脱机 |
| 匿名 IP 地址 | 从匿名 IP 地址登录（例如：Tor 浏览器，匿名程序 VPN）。 | 实时 |
| 不熟悉的登录属性 | 使用给定用户最近未曾出现过的属性进行登录。 | 实时 |
| 受恶意软件感染的 IP 地址 | 从受恶意软件感染的 IP 地址进行登录 | 脱机 |
| 凭据泄漏 | 此风险事件指示用户的有效凭据已泄漏 | 脱机 |





## <a name="types-of-risk"></a>风险类型 

Identity Protection 基于两种风险类型：

- 登录风险

- 用户风险

### <a name="sign-in-risk"></a>登录风险

登录风险表示给定身份验证请求未经标识所有者授权的概率。

有两个登录风险评估： 

- **登录风险（实时）** - 登录风险（实时）基于在登录处理期间触发的所有实时检测。  

- **登录风险（总计）** - 登录风险（总计）是登录的总风险。 它通过机器学习模型进行计算，该模型考虑以下因素：

    - 实时检测（如上所述）
    
    - 脱机检测（登录后即触发） 
    
    - 登录的所有其他功能


### <a name="user-risk"></a>用户风险

用户风险表示给定标识遭入侵的概率。 

用户风险通过考虑与用户相关的所有风险进行计算：

- 所有有风险的登录
- 所有未链接到登录的风险事件
- 当前用户风险
- 到目前为止，对用户采取的任何风险补救或消除措施



## <a name="how-identity-protection-detects-risk"></a>Identity Protection 如何检测风险  

Azure AD 使用机器学习来检测异常和可疑活动，具体使用在登录过程中实时检测到的信号，以及与用户及其登录活动相关的非实时信号。 使用这些数据，Identity Protection 在每次用户进行身份验证时计算实时登录风险，并为每个用户确定整体用户风险级别。 Identity Protection 允许你通过配置 Identity Protection 用户风险和登录风险策略，自动对这些风险检测采取措施。  

 

要理解 Identity Protection 如何检测风险，有两个重要概念：用户风险和登录风险。 登录风险反映了给定身份验证请求未经标识所有者授权的概率。 有两种类型的登录风险：实时登录风险和总登录风险。 实时登录风险在给定登录尝试时进行检测（例如来自匿名 IP 地址的登录）。 总登录风险是检测到的实时登录风险以及与用户登录相关的任何后续非实时风险事件的总计（如无法前往的位置）。 用户风险反映了恶意参与者破坏给定标识的总体可能性。 用户风险包含给定用户的所有风险活动，包括：

- 实时登录风险
- 后续登录风险
- 有风险的用户检测。   

 

 
 ![流向](./media/overview-v2/01.png)
 

 

上图概述了 Identity Protection 风险检测的基线流和对任何给定登录的响应。  

 

 

 

## <a name="common-scenarios"></a>常见方案 

让我们来看一下 Contoso 员工 Sarah 的示例。 

1. Sarah 试图通过 Tor 浏览器登录 Exchange Online。 登录时，Azure AD 检测实时风险事件。 

2. Azure AD 检测到 Sarah 从一个匿名 IP 地址登录，从而触发了中等登录风险级别。 

3. Sarah 收到了 MFA 提示，因为 Contoso 的 IT 管理员配置了 Identity Protection 登录风险条件访问策略。 该策略要求对中等或更高级别的登录风险进行 MFA。 

4. Sarah 通过 MFA 提示并访问了 Exchange Online，而且 Sarah 的用户风险级别没有改变。 

后台发生了什么？ Tor 浏览器的登录尝试因匿名 IP 地址在 Azure AD 中触发了实时登录风险。 Azure AD 在处理请求时，应用了在 Identity Protection 中配置的登录风险策略，因为 Sarah 的登录风险级别达到了阈值（中等）。 由于 Sarah 之前已经注册了 MFA，因此她能够应对并通过 MFA 的考验。 她能够成功通过 MFA 考验向 Azure AD 表明，她很可能是合法的标识所有者，其用户风险级别不会增加。 


但是，如果 Sarah 不是那个试图登录的人呢？ 

1. 持有 Sarah 凭据的恶意参与者试图从 Tor 浏览器登录 Sarah 的 Exchange Online 帐户，因为他们试图隐藏自己的 IP 地址。 

2. Azure AD 检测到登录尝试来自一个匿名 IP 地址，从而触发了实时登录风险。 

3. 恶意参与者会收到 MFA 提示，因为 Contoso 的 IT 管理员配置了 Identity Protection 登录风险条件访问策略，要求在中等或更高级别风险情况下进行 MFA。 

4. 恶意参与者未能通过 MFA 考验，无法访问 Sarah 的 Exchange Online 帐户。 

5. 失败的 MFA 提示触发了要记录的风险事件，进而增加了 Sarah 未来登录的用户风险。 

既然恶意参与者试图访问 Sarah 的帐户，我们来看看下次 Sarah 尝试登录时会发生什么情况。 

1. Sarah 试图通过 Outlook 登录 Exchange Online。 登录时，Azure AD 会检测实时风险事件以及任何以前的用户风险。 

2. Azure AD 没有检测到任何实时登录风险，但检测到由于前面场景中过去的风险活动而导致的高用户风险。  

3. Sarah 收到了密码重置提示，因为 Contoso 的 IT 管理员将 Identity Protection 用户风险策略配置为在高风险用户登录时要求更改密码。 

4. 由于 Sarah 注册了 SSPR 和 MFA，所以她成功重置了密码。 

5. 通过重置密码，Sarah 的凭据不再遭到泄露，其标识也回到了安全状态。 

6. Sarah 之前的风险事件得到了解决，她的用户风险级别自动重置，以作为对缓解凭据泄漏的响应。 

## <a name="how-do-i-configure-identity-protection"></a>如何配置 Identity Protection？ 

若要开始使用 Identity Protection，首先配置用户风险策略和登录风险策略。 配置这些策略并将其应用于测试组后，就可以模拟风险事件，以了解 Identity Protection 将如何在环境中进行响应。 以下快速入门指南分步演示了如何在环境中设置上述策略和测试。 

 

Identity Protection 支持 Azure AD 中的 3 个角色，以平衡围绕部署的管理活动： 

| 角色 | 有权执行的操作 | 无权执行的操作 |
| --- | --- | --- |
| 全局管理员 | 对 Identity Protection 具有完全访问权限、载入 Identity Protection | |
| 安全管理员 | 对 Identity Protection 具有完全访问权限 | 载入 Identity Protection、重置用户密码 |
| 安全读者 | 对 Identity Protection 的只读访问 | 载入 Identity Protection、修改用户、配置策略、重置密码| 

有关更多详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)

 
## <a name="licensing"></a>许可

>[!NOTE]
> 在 Identity Protection 的公共预览版（已刷新）期间，只有 Azure AD Premium P2 客户能够访问有风险的用户报告和有风险的登录报告。



| 功能 | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- |
| 用户风险策略 | 是 | 否 | 否 |
| 登录风险策略 | 是 | 否 | 否 |
| 有风险的用户报告 | 完全访问权限 | 有限信息 | 有限信息 |
| 风险登录报告 | 完全访问权限 | 有限信息 | 有限信息 |
| MFA 注册策略 | 是 | 否 | 否 |







## <a name="next-steps"></a>后续步骤 

若要开始使用 Identity Protection，请参阅[配置登录风险策略](quickstart-sign-in-risk-policy.md)。 






