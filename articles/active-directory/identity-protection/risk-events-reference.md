---
title: Azure Active Directory 标识保护风险事件参考 | Microsoft Docs
description: Azure Active Directory 标识保护风险事件参考。
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
ms.openlocfilehash: 5d2247aab872a71f250bd0b4b52714e402d2102d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905197"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Azure Active Directory 标识保护风险事件参考

大多数安全违规出现在当攻击者通过窃取用户的标识来获取环境的访问权限时。 发现标识是否遭到入侵并不容易。 Azure Active Directory 使用自适应机器学习算法和试探法来检测与用户帐户相关的可疑操作。 每个检测到的可疑操作都存储在称为“风险事件”的记录中。

## <a name="anonymous-ip-address"></a>匿名 IP 地址

**检测类型：** 实时  
**旧名称：** 从匿名 IP 地址登录

此风险事件类型指示从匿名 IP 地址登录（例如，Tor 浏览器，匿名程序 VPN）。
这些 IP 地址通常由希望隐藏其登录遥测数据（IP 地址、位置、设备等）的参与者使用以实现潜在恶意目的。

## <a name="atypical-travel"></a>异常位置登录

**检测类型：** 脱机  
**旧名称：** 不可能前往这些异常位置

此风险事件类型可标识从相距遥远的地理位置进行的两次登录，根据用户以往的行为，其中至少有一个位置属于异常。 除了若干其他因素外，此机器学习算法还考虑两次登录之间相隔的时间以及用户从第一个位置前往第二个位置所需的时间，因为这指示有不同的用户在使用相同的凭据。

此算法会忽略明显的“误报”，从而改善不可能前往条件，例如组织中其他用户定期使用的 VPN 和位置。 系统最早有一个 14 天或 10 次登录的初始学习期限，在此期间它将学习新用户的登录行为。

## <a name="leaked-credentials"></a>凭据泄漏

**检测类型：** 脱机  
**旧名称：** 凭据泄漏的用户

此风险事件类型指示用户的有效凭据已泄漏。
当网络犯罪分子泄露合法用户的有效密码时，他们通常会共享这些凭据。 共享方式通常为将凭据公开发布在暗网或粘贴网站上，或者在黑市上交易或出售凭据。 Microsoft 泄露凭据服务通过监控公网和暗网并与以下机构合作获取用户名/密码对：

- 研究人员
- 执法机构
- Microsoft 安全团队
- 其他受信任的来源

当服务从暗网、粘贴网站或上述来源获取用户凭据时，会根据 Azure AD 用户当前的有效凭据对其进行检查，以找到有效匹配项。

## <a name="malware-linked-ip-address"></a>受恶意软件感染的 IP 地址

**检测类型：** 脱机  
**旧名称：** 从受感染的设备登录

此风险事件类型指示从感染了恶意软件的 IP 地址（即主动与僵尸服务器通信）登录。 这通过分析用户设备的 IP 地址与僵尸服务器活动时连接过僵尸服务器的 IP 地址之间的相互关系可得以确定。

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登录属性

**检测类型：** 实时  
**旧名称：** 从不熟悉的位置登录

此风险事件类型考虑过去的登录历史记录 (IP、纬度/经度和 ASN) 来查找异常登录。系统会存储用户以前的登录位置信息，并将其视为“熟悉”位置。 当从尚未在熟悉位置列表中列出的位置登录时，将触发此风险事件。 新创建的用户在一段时间内将处于 "学习模式", 在这种情况下, 在我们的算法了解用户行为时, 将会关闭风险事件。 学习模式持续时间是动态的, 具体取决于算法收集有关用户登录模式的足够信息所花费的时间。 最短持续时间为五天。 在长时间处于非活动状态后, 用户可以返回到学习模式。 系统还会忽略从常用设备和接近熟悉位置的地理位置进行登录。 

我们还对基本身份验证（或旧版协议）运行此检测。 由于这些协议没有新型属性（如客户端 ID），因此用来减少误报的遥测数据较为有限。 建议客户采用新式身份验证。

## <a name="azure-ad-threat-intelligence"></a>Azure AD 威胁智能

**检测类型：** 脱机 <br>
**旧名称：** 此检测将显示在旧 Azure AD Identity Protection 报表中 (标记为有风险的用户、风险事件) 显示为 "拥有泄露凭据的用户"

此风险事件类型表示对于给定用户异常的用户活动, 或者与基于 Microsoft 内部和外部威胁情报源的已知攻击模式一致。

## <a name="admin-confirmed-user-compromised"></a>管理员已确认用户遭入侵

**检测类型：** 脱机 <br>
此检测表明管理员已在有风险的用户 UI 或使用 riskyUsers API 中选择了 "确认用户泄露"。 若要查看已确认此用户已泄露的管理员, 请检查用户的风险历史记录 (通过 UI 或 API)。
