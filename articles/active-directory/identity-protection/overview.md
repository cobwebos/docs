---
title: Azure Active Directory 标识保护 | Microsoft Docs
description: 了解如何使用 Azure AD 标识保护来限制攻击者利用已泄露标识或设备的能力，以及保护之前怀疑或已知受到威胁的标识或设备。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b89cab41061376fc1d8b4cbffc8fe87b9677688
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125677"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Azure Active Directory 标识保护是什么？

组织使用 Azure Active Directory [标识](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)保护可以配置对检测到的与用户标识相关的可疑操作的自动响应。

## <a name="get-started"></a>入门

Microsoft 在基于云的标识保护方面拥有数十载的经验。 通过 Azure Active Directory 标识保护，可以在环境中使用与 Microsoft 用于保护标识相同的保护系统。

大多数安全违规出现在当攻击者通过窃取用户的标识来获取环境的访问权限时。 近年来，攻击者越来越擅于使用第三方违规，以及使用复杂的网络钓鱼攻击。 攻击者获取较低权限的用户帐户的访问权限后，即可通过横向移动，轻松访问重要的公司资源。

因此需要：

- 保护任何权限级别的所有标识
- 主动防止滥用遭到入侵的标识

发现标识是否遭到入侵并不容易。 Azure Active Directory 使用自适应机器学习算法和试探法来检测异常和可疑事件，这些事件能指示可能遭到入侵的标识。 “标识保护”使用此数据生成报告和警报，使你能够评估检测到的问题并采取相应的缓解措施或补救措施。

Azure Active Directory 标识保护不只是一个监视和报告工具。 若要保护组织的标识，可以配置基于风险的策略，该策略可在达到指定风险级别时自动响应检测到的问题。 除了 Azure Active Directory 与[企业移动性 + 安全性](https://docs.microsoft.com/enterprise-mobility-security/) (EMS) 提供的条件访问控制以外，这些策略也可以自动阻止或启用自适应补救措施，包括密码重置和强制实施多重身份验证。

### <a name="identity-protection-capabilities"></a>“标识保护”功能

**检测漏洞和风险帐户：**  

- 提供自定义建议，通过突显漏洞来改善整体安全局势
- 计算登录风险级别
- 计算用户风险级别

**调查风险检测：**

- 针对风险检测发送通知
- 使用相关的上下文的信息调查风险检测
- 提供基本工作流来跟踪调查
- 轻松使用补救措施，例如密码重置

**基于风险的条件访问策略：**

- 通过阻止登录或需要多重身份验证来降低风险的策略
- 阻止或保护有风险用户帐户的策略
- 要求用户注册多重身份验证的策略

## <a name="identity-protection-roles"></a>标识保护角色

若要对围绕“标识保护”实现展开的管理活动进行负载均衡，可以分配多个角色。 Azure AD 标识保护支持 3 个目录角色：

| 角色 | 有权执行的操作 | 无权执行的操作 |
| :-- | --- | --- |
| 全局管理员 | 对“标识保护”具有完全访问权限、载入“标识保护”| |
| 安全管理员 | 对“标识保护”具有完全访问权限 | 载入“标识保护”、重置用户密码 |
| 安全读取者 | 对“标识保护”的只读访问 | 载入“标识保护”、修改用户、配置策略、重置密码 |

有关更多详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>检测

### <a name="vulnerabilities"></a>漏洞

Azure Active Directory 标识保护分析配置，并检测影响用户标识的漏洞。 有关详细信息，请参阅[“Azure Active Directory 标识保护”检测到的漏洞](vulnerabilities.md)。

### <a name="risk-detections"></a>风险检测

Azure Active Directory 使用自适应机器学习算法和试探法来检测与用户标识相关的可疑操作。 系统为检测到的每个可疑操作创建记录。 这些记录也称为风险检测。  
有关更多详细信息，请参阅 [Azure Active Directory 风险检测](../active-directory-identity-protection-risk-events.md)。

## <a name="investigation"></a>调查

“标识保护”的使用通常从“标识保护”仪表板开始。

![补救](./media/overview/1000.png "补救")

在仪表板中可以访问：

- 报告，例如**已标记为有风险的用户**、**风险检测**和**漏洞**
- 各种设置，例如“安全策略”、“通知”和“多重身份验证注册”的配置   

这通常是调查的起点，在调查过程中查看风险检测相关活动、日志和其他相关信息可以确定是否需要采取补救或缓解措施，了解标识如何受到安全威胁，以及受到安全威胁的标识如何被使用。

可以将调查活动绑定到 Azure Active Directory Protection 发送的每封电子邮件[通知](notifications.md)。

## <a name="policies"></a>策略

为了实现自动响应，Azure Active Directory 标识保护提供了三个策略：

- [多重身份验证注册策略](howto-mfa-policy.md)
- [用户风险策略](howto-user-risk-policy.md)
- [登录风险策略](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>许可要求

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>后续步骤

- [第 9 频道：Azure AD 和标识展示：“标识保护”预览版](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [启用 Azure Active Directory 标识保护](enable.md)
