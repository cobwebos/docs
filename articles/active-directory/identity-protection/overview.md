---
title: Azure Active Directory Identity Protection | Microsoft 文档
description: 了解如何使用 Azure AD Identity Protection 来限制攻击者利用已泄露标识或设备的能力，以及保护之前怀疑或已知受到威胁的标识或设备。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 73b76561829e6d50918881427894ea310920fce6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465186"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection 是什么？

Azure Active Directory Identity Protection 是 Azure AD Premium P2 版本的功能，支持：

- 检测影响组织标识的潜在漏洞

- 配置自动响应，可检测与组织标识相关的可以操作  

- 调查可疑事件，并采取适当的措施解决   


## <a name="get-started"></a>入门

Microsoft 在基于云的标识保护方面拥有数十载的经验。 通过 Azure Active Directory Identity Protection，可以在环境中使用与 Microsoft 用于保护标识相同的保护系统。

大多数安全违规出现在当攻击者通过窃取用户的标识来获取环境的访问权限时。 近年来，攻击者越来越擅于使用第三方违规，以及使用复杂的网络钓鱼攻击。 攻击者获取较低权限的用户帐户的访问权限后，即可通过横向移动，轻松访问重要的公司资源。

因此需要：

- 保护任何权限级别的所有标识

- 主动防止滥用遭到入侵的标识

发现标识是否遭到入侵并不容易。 Azure Active Directory 使用自适应机器学习算法和试探法来检测异常和可疑事件，这些事件能指示可能遭到入侵的标识。 Identity Protection 使用此数据生成报告和警报，使你能够评估检测到的问题并采取相应的缓解措施或补救措施。

Azure Active Directory Identity Protection 不只是一个监视和报告工具。 若要保护组织的标识，可以配置基于风险的策略，该策略可在达到指定风险级别时自动响应检测到的问题。 除了 Azure Active Directory 与 EMS 提供的条件性访问控制以外，这些策略也可以自动阻止或启用自适应补救措施，包括密码重置和强制实施多重身份验证。


#### <a name="identity-protection-capabilities"></a>Identity Protection 功能

**检测漏洞和风险帐户：**  

* 提供自定义建议，通过突显漏洞来改善整体安全局势
* 计算登录风险级别
* 计算用户风险级别


**调查风险事件：**

* 针对风险事件发送通知
* 使用相关的区分上下文的信息调查风险事件
* 提供基本工作流来跟踪调查
* 轻松使用补救措施，例如密码重置

**基于风险的条件性访问策略：**

* 通过阻止登录或需要多重身份验证来降低风险的策略
* 阻止或保护有风险用户帐户的策略
* 要求用户注册多重身份验证的策略



## <a name="identity-protection-roles"></a>标识保护角色

若要对围绕 Identity Protection 实现展开的管理活动进行负载均衡，可以分配多个角色。 Azure AD Identity Protection 支持 3 个目录角色：

| 角色                         | 有权执行的操作                          | 无权执行的操作
| :--                          | ---                                |  ---   |
| 全局管理员         | 对 Identity Protection 具有完全访问权限、载入 Identity Protection| |
| 安全管理员       | 对 Identity Protection 具有完全访问权限 | 载入 Identity Protection、重置用户密码 |
| 安全读者              | 对 Identity Protection 的只读访问 | 载入 Identity Protection、修改用户、配置策略、重置密码 |




有关更多详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>检测

### <a name="vulnerabilities"></a>漏洞

Azure Active Directory Identity Protection 分析配置，并检测影响用户标识的漏洞。 有关详细信息，请参阅 [Azure Active Directory Identity Protection 检测到的漏洞](vulnerabilities.md)。

### <a name="risk-events"></a>风险事件

Azure Active Directory 使用自适应机器学习算法和试探法来检测与用户标识相关的可疑操作。 系统为检测到的每个可疑操作创建记录。 这些记录也称为风险事件。  
有关详细信息，请参阅 [Azure Active Directory 风险事件](../active-directory-identity-protection-risk-events.md)。


## <a name="investigation"></a>调查

Identity Protection 的使用通常从 Identity Protection 仪表板开始。

![补救](./media/overview/1000.png "补救")

在仪表板中可以访问：

* “已标记为存在风险的用户”、“风险事件”和“漏洞”等报告
* 各种设置，例如“安全策略”、“通知”和“多重身份验证注册”的配置

这通常是调查的起点，在调查过程中查看风险事件相关活动、日志和其他相关信息可以确定是否需要采取补救或缓解措施，了解标识如何遭到入侵，以及遭到入侵的标识如何被利用。

可以将调查活动绑定到 Azure Active Directory Protection 发送的每封电子邮件[通知](notifications.md)。



## <a name="policies"></a>策略

为了实现自动响应，Azure Active Directory Identity Protection 提供了三个策略：

- [多重身份验证注册策略](howto-mfa-policy.md)

- [用户风险策略](howto-user-risk-policy.md)

- [登录风险策略](howto-sign-in-risk-policy.md)


## <a name="next-steps"></a>后续步骤

- [第 9 频道：Azure AD 和标识展示：Identity Protection 预览版](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [启用 Azure Active Directory Identity Protection](enable.md)

