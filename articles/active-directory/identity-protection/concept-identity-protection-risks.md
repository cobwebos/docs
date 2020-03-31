---
title: 什么是风险？ Azure AD 标识保护
description: 在 Azure AD 标识保护中解释风险
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
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253684"
---
# <a name="what-is-risk"></a>什么是风险？

Azure AD 标识保护中的风险检测包括与目录中用户帐户相关的任何已识别的可疑操作。

身份保护使组织能够访问强大的资源，以便查看和快速响应这些可疑操作。 

![显示风险用户和登录的安全概述](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>风险类型和检测

有两种类型的风险**用户**和**登录**和两种类型的检测或计算**实时**和**离线**。

### <a name="user-risk"></a>用户风险

用户风险表示给定标识或帐户受到威胁的可能性。 

这些风险是使用 Microsoft 的内部和外部威胁情报来源（包括安全研究人员、执法专业人员、Microsoft 的安全团队和其他可信来源）离线计算的。

| 风险检测 | 描述 |
| --- | --- |
| 已泄漏凭据 | 此风险检测类型指示用户的有效凭据已泄露。 当网络犯罪分子泄露合法用户的有效密码时，他们通常会共享这些凭据。 这种共享通常是通过在黑网上公开发布、粘贴网站或在黑市上买卖凭据来完成的。 当 Microsoft 泄露的凭据服务从暗网、粘贴网站或其他源获取用户凭据时，将对照 Azure AD 用户当前的有效凭据检查这些凭据以查找有效的匹配项。 |
| Azure AD 威胁智能 | 此风险检测类型指示给定用户异常或与基于 Microsoft 内部和外部威胁情报源的已知攻击模式一致的用户活动。 |

### <a name="sign-in-risk"></a>登录风险

登录风险表示给定身份验证请求未由标识所有者授权的概率。 

这些风险可以通过 Microsoft 的内部和外部威胁情报来源（包括安全研究人员、执法专业人员、Microsoft 的安全团队和其他可信来源）实时计算或离线计算。

| 风险检测 | 检测类型 | 描述 |
| --- | --- | --- |
| 匿名 IP 地址 | 实时 | 此风险检测类型指示来自匿名 IP 地址（例如，Tor 浏览器或匿名 VPN）的登录。 这些 IP 地址通常由希望隐藏其登录遥测数据（IP 地址、位置、设备等）的参与者使用以实现潜在恶意目的。 |
| 异常位置登录 | Offline | 此风险检测类型可标识从相距遥远的地理位置进行的两次登录，根据用户以往的行为，其中至少有一个位置属于异常。 除了若干其他因素外，此机器学习算法还考虑两次登录之间相隔的时间以及用户从第一个位置前往第二个位置所需的时间，因为这指示有不同的用户在使用相同的凭据。 <br><br> 此算法会忽略明显的“误报”，从而改善不可能前往条件，例如组织中其他用户定期使用的 VPN 和位置。 系统的初始学习期最早为 14 天或 10 天，在此期间，系统会学习新用户的登录行为。 |
| 受恶意软件感染的 IP 地址 | Offline | 此风险检测类型指示从感染了恶意软件的 IP 地址（即主动与僵尸服务器通信）登录。 此检测由将用户设备的 IP 地址与在机器人服务器处于活动状态时与机器人服务器接触的 IP 地址相关联来确定。 |
| 不熟悉的登录属性 | 实时 | 此风险检测类型考虑过去的登录历史记录（IP、纬度/经度和 ASN）来查找异常登录。系统存储有关用户使用以前的位置的信息，并考虑这些"熟悉"的位置。 当从尚未在熟悉位置列表中列出的位置登录时，将触发此风险检测。 新创建的用户将在"学习模式"中运行一段时间，在我们的算法了解用户的行为时，将关闭不熟悉的登录属性风险检测。 学习模式持续时间是动态的，取决于算法收集有关用户登录模式的足够信息所需的时间。 最短持续时间为五天。 用户可以在长时间处于非活动状态后返回到学习模式。 系统还会忽略从常用设备和接近熟悉位置的地理位置进行登录。 <br><br> 我们还对基本身份验证（或旧版协议）运行此检测。 由于这些协议没有新型属性（如客户端 ID），因此用来减少误报的遥测数据较为有限。 建议客户采用新式身份验证。 |
| 管理员确认用户遭入侵 | Offline | 此检测指示管理员已在风险用户 UI 或使用风险用户 API 中选择"确认用户受损"。 要查看哪个管理员已确认此用户已泄露，请检查用户的风险历史记录（通过 UI 或 API）。 |
| 恶意 IP 地址 | Offline | 此检测表明登录是通过恶意 IP 地址进行的。 由于从 IP 地址或其他 IP 信誉源接收的凭据无效，因此基于高失败率，IP 地址被视为恶意地址。 |
| 可疑的收件箱操作规则 | Offline | 此检测由[微软云应用安全 （MCAS）](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)发现。 这项检测会对环境进行分析，并在用户收件箱中设置删除或移动邮件或文件夹的可疑规则时触发警报。 这可能表示用户帐户已泄露，邮件被有意隐藏，并且邮箱正用于在组织中分发垃圾邮件或恶意软件。 |
| Impossible travel | Offline | 此检测由[微软云应用安全 （MCAS）](/cloud-app-security/anomaly-detection-policy#impossible-travel)发现。 此项检测可识别来源于保持一定地理距离的位置的两个用户活动（一个或多个会话），而完成这两个活动的时间段短于该用户从第一个位置旅行到第二个位置所需的时间，这表示另一个用户在使用相同的凭据。 |

### <a name="other-risk-detections"></a>其他风险检测

| 风险检测 | 检测类型 | 描述 |
| --- | --- | --- |
| 检测到其他风险 | 实时或脱机 | 此检测表明已检测到上述某个高级检测。 由于高级检测仅对 Azure AD 高级 P2 客户可见，因此对于没有 Azure AD 高级 P2 许可证的客户，这些检测名为"检测到的额外风险"。 |

## <a name="next-steps"></a>后续步骤

- [可用于规避风险的策略](concept-identity-protection-policies.md)

- [安全概述](concept-identity-protection-security-overview.md)
