---
title: Azure Active Directory 标识保护术语表 | Microsoft Docs
description: Azure Active Directory 标识保护术语表
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1a1c35929670891c38bb1b3e26806b4c9bed6a0
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126465"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory 标识保护术语表

### <a name="at-risk-user"></a>有风险（用户）
具有一个或多个活动的风险检测的用户。 

### <a name="atypical-sign-in-location"></a>异常登录位置
从特定用户、类似用户或租户不常用的地理位置登录。

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Azure Active Directory 的一种安全模块, 可提供对风险检测的综合视图, 以及影响组织标识的潜在漏洞。

### <a name="conditional-access"></a>条件访问
用于保护资源访问的策略。 条件访问规则存储在 Azure Active Directory 中，在授予资源访问权限之前由 Azure AD 评估。  示例规则包括根据用户的位置、设备运行状况或用户身份验证方法来限制访问。

### <a name="credentials"></a>凭据
包含标识以及用于获取访问本地和网络资源的身份证明的信息。 凭据的示例包括用户名和密码、智能卡与证书。

### <a name="event"></a>Event
Azure Active Directory 中的活动记录。

### <a name="false-positive-risk-detection"></a>"假" (风险检测)
由 Identity Protection 用户手动设置的风险检测状态, 指示风险检测已调查并错误地标记为风险检测。

### <a name="identity"></a>标识
必须通过基于条件（例如密码或证书等）的身份验证方式来验证个人或实体。

### <a name="identity-risk-detection"></a>标识风险检测
“标识保护”标记为异常的 AAD 事件，可能表示标识已遭入侵。

### <a name="ignored-risk-detection"></a>已忽略 (风险检测)
由 Identity Protection 用户手动设置的风险检测状态, 指示在不采取修正操作的情况下关闭了风险检测。

### <a name="impossible-travel-from-atypical-locations"></a>不可能来自异常位置
检测到相同用户的两个登录, 其中至少有一个登录位置来自非典型登录位置, 并且登录之间的时间短于在这些位置之间进行物理传输所需的最短时间, 则会触发风险检测机构.  

### <a name="investigation"></a>调查
查看与风险检测相关的活动、日志和其他相关信息, 以确定是否需要修正或缓解步骤的过程, 了解身份是否安全以及如何泄露已使用标识。

### <a name="leaked-credentials"></a>泄漏的凭据
当发现当前用户凭据 (用户名和密码) 通过我们的研究人员公开发布时, 会触发风险检测。

### <a name="mitigation"></a>缓解
限制或消除攻击者利用遭到入侵的标识或设备的能力，且无需将标识或设备还原到安全状态。 缓解措施不能解决以前与标识或设备关联的风险检测。

### <a name="multi-factor-authentication"></a>多重身份验证
要求采用两种或更多种身份验证方法，其中可能包括用户拥有的事物（例如证书）、用户知道的事物（例如用户名、密码或通行短语）、物体属性（例如指纹）以及个人属性（例如个人签名）。

### <a name="offline-detection"></a>脱机检测
针对已发生的事件检测异常并评估事件的风险（例如事后的登录尝试）。

### <a name="policy-condition"></a>策略条件
安全策略的一部分，定义策略中包含或排除的实体（组、用户、应用、设备平台、设备状态、IP 范围、客户端类型）。

### <a name="policy-rule"></a>策略规则
安全策略的一部分，说明触发此策略的情况，以及策略触发时采取的措施。

### <a name="prevention"></a>预防
防止通过滥用疑似或已知遭到入侵的标识或设备来损害组织的措施。 阻止操作不保护设备或标识, 并且不能解决以前的风险检测。

### <a name="privileged-user"></a>特权（用户）
在出现风险检测时, 具有对 Azure Active Directory 中的一个或多个资源的永久或临时管理权限的用户, 例如全局管理员、计费管理员、服务管理员、用户管理员和密码管理. 

### <a name="real-time"></a>实时
请参阅“实时检测”。

### <a name="real-time-detection"></a>实时检测
在允许事件继续进行之前，检测异常并评估事件的风险（例如登录尝试）。

### <a name="remediated-risk-detection"></a>修正 (风险检测)
由 Identity Protection 自动设置的风险检测状态, 指示使用此类型的风险检测的标准修正操作来修正风险检测。 例如, 重置用户密码时, 会自动修正许多表明以前密码被泄露的风险检测。

### <a name="remediation"></a>更正
保护以前疑似或已知遭到入侵的标识或设备的措施。 更正操作将标识或设备还原到安全状态, 并解决与标识或设备关联的以前的风险检测。

### <a name="resolved-risk-detection"></a>已解决 (风险检测)
由 Identity Protection 用户手动设置的风险检测状态, 指示用户在 Identity Protection 之外采取了适当的补救措施, 并且应该将风险检测视为已关闭。

### <a name="risk-detection-status"></a>风险检测状态
风险检测的属性, 指示事件是否处于活动状态, 如果已关闭, 则指示关闭事件的原因。

### <a name="risk-detection-type"></a>风险检测类型
风险检测的类别, 指示导致事件被视为危险的异常类型。

### <a name="risk-level-risk-detection"></a>风险级别 (风险检测)
风险检测的严重性 (高、中或低) 的指示, 用于帮助标识保护用户确定他们为了降低组织风险而采取的措施的优先级。 

### <a name="risk-level-sign-in"></a>风险级别（登录）
指示特定登录（其他某人尝试使用用户的标识）的可能性（高、中或低）。

### <a name="risk-level-user-compromise"></a>风险级别（用户入侵）
指示标识遭到入侵的可能性（高、中或低）。

### <a name="risk-level-vulnerability"></a>风险级别（漏洞）
指示漏洞的严重性（高、中或低），可帮助“标识保护”用户指定为了降低组织风险而采取的措施的优先级。

### <a name="secure-identity"></a>安全（标识）
采取补救措施（例如更改密码或者重建计算机映像），将可能遭到入侵的标识还原到未遭入侵的状态。

### <a name="security-policy"></a>安全策略
策略规则和条件的集合。 策略可应用到各种实体，例如用户、组、应用、设备、设备平台、设备状态、IP 范围和 Auth2.0 客户端类型。 启用某个策略后，会在向策略包含的实体颁发资源令牌时评估该策略。

### <a name="sign-in-v"></a>登录（动词）
在 Azure Active Directory 中对某个标识进行身份验证。

### <a name="sign-in-n"></a>登录（名词）
在 Azure Active Directory 中对某个标识进行身份验证的过程或操作，以及捕获此操作的事件。

### <a name="sign-in-from-anonymous-ip-address"></a>从匿名 IP 地址登录
从已被标识为匿名代理 IP 地址的 IP 地址成功登录之后触发的风险检测。

### <a name="sign-in-from-infected-device"></a>从受感染的设备登录
当登录来自某个 IP 地址时, 将会触发风险检测, 该 IP 地址已被一个或多个受损设备使用, 这些设备正在积极尝试与机器人服务器进行通信。

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>从具有可疑活动的 IP 地址登录
在一段很短的时间内通过多个用户帐户的 IP 地址成功登录后触发的风险检测。

### <a name="sign-in-from-unfamiliar-location"></a>从不熟悉的位置登录
用户从新位置 (IP、纬度/经度和 ASN) 成功登录时触发的风险检测。

### <a name="sign-in-risk"></a>登录风险
请参阅“风险级别（登录）”

### <a name="sign-in-risk-policy"></a>登录风险策略
一个条件访问策略，可评估特定登录的风险，根据预先定义的条件和规则来应用缓解措施。

### <a name="user-compromise-risk"></a>用户泄漏风险
请参阅“风险级别（用户入侵）”

### <a name="user-risk"></a>用户风险
请参阅“风险级别（用户入侵）”。

### <a name="user-risk-policy"></a>用户风险策略
一个条件访问策略，可根据预先定义的条件和规则来评估登录并应用缓解措施。

### <a name="users-flagged-for-risk"></a>已标记为存在风险的用户
具有活动或已修正的风险检测的用户

### <a name="vulnerability"></a>漏洞
Azure Active Directory 中导致目录容易遭到入侵或威胁的配置或状态。

## <a name="see-also"></a>请参阅
* [Azure Active Directory 标识保护](../active-directory-identityprotection.md)
