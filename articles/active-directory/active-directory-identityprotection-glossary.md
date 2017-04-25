---
title: "Azure Active Directory Identity Protection 术语表 | Microsoft 文档"
description: "Azure Active Directory Identity Protection 术语表"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略, 术语表"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f31188ed3216034ef8ec056023472844ec4debd6
ms.lasthandoff: 12/29/2016


---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection 术语表
### <a name="at-risk-user"></a>有风险（用户）
具有一个或多个活动风险事件的用户。 

### <a name="atypical-sign-in-location"></a>异常登录位置
从特定用户、类似用户或租户不常用的地理位置登录。

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Azure Active Directory 的安全模块，提供合并的视图用于洞察影响组织标识的风险事件和潜在漏洞。

### <a name="conditional-access"></a>条件性访问
用于保护资源访问的策略。 条件性访问规则存储在 Azure Active Directory 中，在授予资源访问权限之前由 Azure AD 评估。  示例规则包括根据用户的位置、设备运行状况或用户身份验证方法来限制访问。

### <a name="credentials"></a>凭据
包含标识以及用于获取访问本地和网络资源的身份证明的信息。 凭据的示例包括用户名和密码、智能卡与证书。

### <a name="event"></a>事件
Azure Active Directory 中的活动记录。

### <a name="false-positive-risk-event"></a>误报（风险事件）
由 Identity Protection 用户手动设置的风险事件状态，表示此风险事件经过调查，被错误地标记为风险事件。

### <a name="identity"></a>标识
必须通过基于条件（例如密码或证书等）的身份验证方式来验证个人或实体。

### <a name="identity-risk-event"></a>标识风险事件
Identity Protection 标记为异常的 AAD 事件，可能表示标识已遭入侵。

### <a name="ignored-risk-event"></a>已忽略（风险事件）
Identity Protection 用户手动设置的风险事件状态，表示此风险事件已关闭，无需采取补救措施。

### <a name="impossible-travel-from-atypical-locations"></a>不可能来自异常位置
检测到同一用户两次登录时触发的风险事件，其中至少一次登录来自异常登录位置，并且登录的间隔时间比在两地之间实际移动所需的最短时间还要短。  

### <a name="investigation"></a>调查
检查风险事件相关活动、日志和其他相关信息的过程，确定是否需要采取补救或缓解措施，了解标识如何遭到入侵，以及遭到入侵的标识如何被利用。

### <a name="leaked-credentials"></a>已泄漏凭据
研究人员发现当前用户凭据（用户名和密码）公开发布在黑暗网络 (Dark Web) 时触发的风险事件。

### <a name="mitigation"></a>缓解措施
限制或消除攻击者利用遭到入侵的标识或设备的能力，且无需将标识或设备还原到安全状态。 缓解措施不能解决以前与标识或设备关联的风险事件。

### <a name="multi-factor-authentication"></a>多重身份验证
要求采用两种或更多种身份验证方法，其中可能包括用户拥有的事物（例如证书）、用户知道的事物（例如用户名、密码或通行短语）、物体属性（例如指纹）以及个人属性（例如个人签名）。

### <a name="offline-detection"></a>脱机检测
针对已发生的事件检测异常并评估事件的风险（例如事后的登录尝试）。

### <a name="policy-condition"></a>策略条件
安全策略的一部分，定义策略中包含或排除的实体（组、用户、应用、设备平台、设备状态、IP 范围、客户端类型）。

### <a name="policy-rule"></a>策略规则
安全策略的一部分，说明触发此策略的情况，以及策略触发时采取的措施。

### <a name="prevention"></a>预防
防止通过滥用疑似或已知遭到入侵的标识或设备来损害组织的措施。 预防措施无法保护设备或标识的安全，且无法解决以前的风险事件。

### <a name="privileged-user"></a>特权（用户）
发生风险事件时，拥有永久或暂时性管理权限，可访问 Azure Active Directory 中一个或多个资源的用户，例如全局管理员、计费管理员、服务管理员、用户管理员和密码管理员。 

### <a name="real-time"></a>实时
请参阅“实时检测”。

### <a name="real-time-detection"></a>实时检测
在允许事件继续进行之前，检测异常并评估事件的风险（例如登录尝试）。

### <a name="remediated-risk-event"></a>已补救（风险事件）
Identity Protection 自动设置的风险事件状态，指示已使用此风险事件类型的标准补救措施来补救此风险事件。 例如，当用户密码重置时，指示以前的密码已遭入侵的风险事件可自动补救。

### <a name="remediation"></a>补救
保护以前疑似或已知遭到入侵的标识或设备的措施。 补救措施可让标识或设备还原到安全状态，解决以前与标识或设备关联的风险事件。

### <a name="resolved-risk-event"></a>已解决（风险事件）
Identity Protection 用户手动设置的风险事件状态，指示用户已在 Identity Protection 外部采取适当的补救措施，应该将风险事件视为已关闭。

### <a name="risk-event-status"></a>风险事件状态
风险事件的属性，指示事件是否处于活动状态，如果已关闭，则指示其关闭原因。

### <a name="risk-event-type"></a>风险事件类型
风险事件的类别，指示导致事件被视为有风险的异常类型。

### <a name="risk-level-risk-event"></a>风险级别（风险事件）
指示风险事件的严重性（高、中或低），可帮助 Identity Protection 用户指定为了降低组织风险而采取的措施的优先级。 

### <a name="risk-level-sign-in"></a>风险级别（登录）
指示特定登录（其他某人尝试使用用户的标识）的可能性（高、中或低）。

### <a name="risk-level-user-compromise"></a>风险级别（用户入侵）
指示标识遭到入侵的可能性（高、中或低）。

### <a name="risk-level-vulnerability"></a>风险级别（漏洞）
指示漏洞的严重性（高、中或低），可帮助 Identity Protection 用户指定为了降低组织风险而采取的措施的优先级。

### <a name="secure-identity"></a>安全（标识）
采取补救措施（例如更改密码或者重建计算机映像），将可能遭到入侵的标识还原到未遭入侵的状态。

### <a name="security-policy"></a>安全策略
策略规则和条件的集合。 策略可应用到各种实体，例如用户、组、应用、设备、设备平台、设备状态、IP 范围和 Auth2.0 客户端类型。 启用某个策略后，将在向策略包含的实体颁发资源令牌时评估该策略。

### <a name="sign-in-v"></a>登录（动词）
在 Azure Active Directory 中对某个标识进行身份验证。

### <a name="sign-in-n"></a>登录（名词）
在 Azure Active Directory 中对某个标识进行身份验证的过程或操作，以及捕获此操作的事件。

### <a name="sign-in-from-anonymous-ip-address"></a>从匿名 IP 地址登录
从标识为匿名代理 IP 地址的 IP 地址成功登录之后触发的风险事件。

### <a name="sign-in-from-infected-device"></a>从受感染的设备登录
当登录源自已知由一个或多个遭入侵设备（此类设备主动尝试与 Bot 服务器通信）使用的 IP 地址时触发的风险事件。

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>从具有可疑活动的 IP 地址登录
在时间内通过多个用户帐户多次尝试登录失败的 IP 地址成功登录之后触发的风险事件。

### <a name="sign-in-from-unfamiliar-location"></a>从不熟悉的位置登录
当用户从新位置（IP、经纬度和 ASN）成功登录时触发的风险事件。

### <a name="sign-in-risk"></a>登录风险
请参阅“风险级别（登录）”

### <a name="sign-in-risk-policy"></a>登录风险策略
一个条件性访问策略，可评估特定登录的风险，根据预先定义的条件和规则来应用缓解措施。

### <a name="user-compromise-risk"></a>用户泄漏风险
请参阅“风险级别（用户入侵）”

### <a name="user-risk"></a>用户风险
请参阅“风险级别（用户入侵）”。

### <a name="user-risk-policy"></a>用户风险策略
一个条件性访问策略，可根据预先定义的条件和规则来评估登录并应用缓解措施。

### <a name="users-flagged-for-risk"></a>已标记为存在风险的用户
存在活动或已补救的风险事件的用户

### <a name="vulnerability"></a>漏洞
Azure Active Directory 中导致目录容易遭到入侵或威胁的配置或状态。

## <a name="see-also"></a>另请参阅
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


