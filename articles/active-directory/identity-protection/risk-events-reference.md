---
title: Azure Active Directory Identity Protection 风险检测参考 |Microsoft Docs
description: Azure Active Directory Identity Protection 风险检测参考。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1005ac1853568222dea83a0f12293945825cf6d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127581"
---
# <a name="azure-active-directory-identity-protection-risk-detections-reference"></a>Azure Active Directory Identity Protection 风险检测参考

大多数安全违规出现在当攻击者通过窃取用户的标识来获取环境的访问权限时。 发现标识是否遭到入侵并不容易。 Azure Active Directory 使用自适应机器学习算法和试探法来检测与用户帐户相关的可疑操作。 每个检测到的可疑操作都存储在称为风险检测的记录中。

## <a name="anonymous-ip-address"></a>匿名 IP 地址

**检测类型：** 实时  
**旧名称：** 从匿名 IP 地址登录

此风险检测类型表示来自匿名 IP 地址的登录 (例如, Tor browser、anonymizer Vpn)。
这些 IP 地址通常由希望隐藏其登录遥测数据（IP 地址、位置、设备等）的参与者使用以实现潜在恶意目的。

## <a name="atypical-travel"></a>异常位置登录

**检测类型：** 脱机  
**旧名称：** 不可能前往这些异常位置

此风险检测类型标识来自地理位置遥远位置的两个登录, 其中至少有一个位置对于用户来说也是典型的, 但前提是过去的行为。 除了若干其他因素外，此机器学习算法还考虑两次登录之间相隔的时间以及用户从第一个位置前往第二个位置所需的时间，因为这指示有不同的用户在使用相同的凭据。

此算法会忽略明显的“误报”，从而改善不可能前往条件，例如组织中其他用户定期使用的 VPN 和位置。 系统最早有一个 14 天或 10 次登录的初始学习期限，在此期间它将学习新用户的登录行为。

## <a name="leaked-credentials"></a>凭据泄漏

**检测类型：** 脱机  
**旧名称：** 凭据泄漏的用户

此风险检测类型表示用户的有效凭据已泄漏。
当网络犯罪分子泄露合法用户的有效密码时，他们通常会共享这些凭据。 共享方式通常为将凭据公开发布在暗网或粘贴网站上，或者在黑市上交易或出售凭据。 Microsoft 泄露凭据服务通过监控公网和暗网并与以下机构合作获取用户名/密码对：

- 研究人员
- 执法机构
- Microsoft 安全团队
- 其他受信任的来源

当服务从暗网、粘贴网站或上述来源获取用户凭据时，会根据 Azure AD 用户当前的有效凭据对其进行检查，以找到有效匹配项。

## <a name="malware-linked-ip-address"></a>受恶意软件感染的 IP 地址

**检测类型：** 脱机  
**旧名称：** 从受感染的设备登录

此风险检测类型表示来自受恶意软件感染的 IP 地址的登录, 该恶意软件与机器人服务器主动通信。 这通过分析用户设备的 IP 地址与僵尸服务器活动时连接过僵尸服务器的 IP 地址之间的相互关系可得以确定。

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登录属性

**检测类型：** 实时  
**旧名称：** 从不熟悉的位置登录

此风险检测类型考虑过去的登录历史记录 (IP、纬度/经度和 ASN), 以查找异常登录。系统会存储用户以前的登录位置信息，并将其视为“熟悉”位置。 当从不在熟悉位置列表中的位置进行登录时, 将触发风险检测。 新创建的用户在一段时间内将处于 "学习模式", 在这种情况下, 在我们的算法了解到用户的行为时, 将会关闭风险检测。 学习模式持续时间是动态的，取决于算法收集足够的用户登录模式信息所需的时间。 最短持续时间为五天。 用户可以在长时间处于非活动状态后返回到学习模式。 系统还会忽略从常用设备和接近熟悉位置的地理位置进行登录。 

我们还对基本身份验证（或旧版协议）运行此检测。 由于这些协议没有新型属性（如客户端 ID），因此用来减少误报的遥测数据较为有限。 建议客户采用新式身份验证。

## <a name="azure-ad-threat-intelligence"></a>Azure AD 威胁智能

**检测类型：** 脱机 <br>
**旧名称：** 此检测将显示在旧 Azure AD Identity Protection 报表中 (标记为有风险的用户、风险检测) 显示为 "拥有泄露凭据的用户"

此风险检测类型表示对于给定用户异常的用户活动, 或者与基于 Microsoft 内部和外部威胁智能源的已知攻击模式一致。

## <a name="admin-confirmed-user-compromised"></a>管理员已确认用户遭入侵

**检测类型：** 脱机 <br>
此检测表明管理员已在有风险的用户 UI 或使用 riskyUsers API 中选择了 "确认用户泄露"。 若要查看已确认此用户已泄露的管理员, 请检查用户的风险历史记录 (通过 UI 或 API)。

## <a name="malicious-ip-address"></a>恶意 IP 地址

**检测类型：** 脱机 <br>
此检测表明从恶意的 IP 地址登录。 IP 地址被视为恶意的, 具体取决于以下各项:
-   高失败率 (由于从 IP 地址接收到无效凭据)
-   其他 IP 信誉源

## <a name="additional-risk-detected"></a>“检测到额外风险”信息气泡

**检测类型：** 实时或脱机 <br>
此检测表明检测到上述高级检测之一。 由于高级检测仅对 Azure AD Premium P2 客户可见, 因此, 对于非 P2 客户, 其标题为 "检测到其他风险"。
