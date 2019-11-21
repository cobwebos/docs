---
title: Azure Active Directory 标识保护术语表
description: Azure Active Directory 标识保护术语表
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232346"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory 标识保护术语表

### <a name="at-risk-user"></a>有风险（用户）
A user with one or more active risk detections. 

### <a name="atypical-sign-in-location"></a>异常登录位置
从特定用户、类似用户或租户不常用的地理位置登录。

### <a name="azure-ad-identity-protection"></a>Azure AD 标识保护
A security module of Azure Active Directory that provides a consolidated view into risk detections and potential vulnerabilities affecting an organization’s identities.

### <a name="conditional-access"></a>条件访问
用于保护资源访问的策略。 Conditional Access rules are stored in the Azure Active Directory and are evaluated by Azure AD before granting access to the resource.  示例规则包括根据用户的位置、设备运行状况或用户身份验证方法来限制访问。

### <a name="credentials"></a>凭据
包含标识以及用于获取访问本地和网络资源的身份证明的信息。 凭据的示例包括用户名和密码、智能卡与证书。

### <a name="event"></a>事件
Azure Active Directory 中的活动记录。

### <a name="false-positive-risk-detection"></a>False-positive (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection was investigated and was incorrectly flagged as a risk detection.

### <a name="identity"></a>身份标识
必须通过基于条件（例如密码或证书等）的身份验证方式来验证个人或实体。

### <a name="identity-risk-detection"></a>Identity risk detection
Azure AD event that was flagged as anomalous by Identity Protection, and may indicate that an identity has been compromised.

### <a name="ignored-risk-detection"></a>Ignored (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection is closed without taking a remediation action.

### <a name="impossible-travel-from-atypical-locations"></a>不可能来自异常位置
A risk detection triggered when two sign-ins for the same user are detected, where at least one of them is from an atypical sign-in location, and where the time between the sign-ins is shorter than the minimum time it would take to physically travel between these locations.  

### <a name="investigation"></a>调查
The process of reviewing the activities, logs, and other relevant information related to a risk detection to decide whether remediation or mitigation steps are necessary, understand if and how the identity was compromised, and understand how the compromised identity was used.

### <a name="leaked-credentials"></a>已泄漏凭据
A risk detection triggered when current user credentials (user name and password) are found posted publicly in the Dark   web by our researchers.

### <a name="mitigation"></a>缓解措施
限制或消除攻击者利用遭到入侵的标识或设备的能力，且无需将标识或设备还原到安全状态。 A mitigation does not resolve previous risk detections associated with the identity or device.

### <a name="multi-factor-authentication"></a>多重身份验证
要求采用两种或更多种身份验证方法，其中可能包括用户拥有的事物（例如证书）、用户知道的事物（例如用户名、密码或通行短语）、物体属性（例如指纹）以及个人属性（例如个人签名）。

### <a name="offline-detection"></a>脱机检测
针对已发生的事件检测异常并评估事件的风险（例如事后的登录尝试）。

### <a name="policy-condition"></a>策略条件
安全策略的一部分，定义策略中包含或排除的实体（组、用户、应用、设备平台、设备状态、IP 范围、客户端类型）。

### <a name="policy-rule"></a>策略规则
The part of a security policy that describes the circumstances that would trigger the policy, and the actions taken when the policy is triggered.

### <a name="prevention"></a>预防
防止通过滥用疑似或已知遭到入侵的标识或设备来损害组织的措施。 A prevention action does not secure the device or identity, and does not resolve previous risk detections.

### <a name="privileged-user"></a>特权（用户）
A user that at the time of a risk detection, had permanent or temporary admin permissions to one or more resources in Azure Active Directory, such as a Global Administrator, Billing Administrator, Service Administrator, User administrator, and Password Administrator. 

### <a name="real-time"></a>实时
请参阅“实时检测”。

### <a name="real-time-detection"></a>实时检测
在允许事件继续进行之前，检测异常并评估事件的风险（例如登录尝试）。

### <a name="remediated-risk-detection"></a>Remediated (risk detection)
A risk detection status set automatically by Identity Protection, indicating that the risk detection was remediated using the standard remediation action for this type of risk detection. For example, when the user password is reset, many risk detections that indicate that the previous password was compromised are automatically remediated.

### <a name="remediation"></a>补救
保护以前疑似或已知遭到入侵的标识或设备的措施。 A remediation action restores the identity or device to a safe state, and resolves previous risk detections associated with the identity or device.

### <a name="resolved-risk-detection"></a>Resolved (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the user took an appropriate remediation action outside Identity Protection, and that the risk detection should be considered closed.

### <a name="risk-detection-status"></a>Risk detection status
A property of a risk detection, indicating whether the event is active, and if closed, the reason for closing it.

### <a name="risk-detection-type"></a>风险检测类型
A category for the risk detection, indicating the type of anomaly that caused the event to be considered risky.

### <a name="risk-level-risk-detection"></a>Risk level (risk detection)
An indication (High, Medium, or Low) of the severity of the risk detection to help Identity Protection users prioritize the actions they take to reduce the risk to their organization. 

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
A risk detection triggered after a successful sign-in from IP address that has been identified as an anonymous proxy IP address.

### <a name="sign-in-from-infected-device"></a>从受感染的设备登录
A risk detection triggered when a sign-in originates from an IP address, which is known to be used by one or more compromised devices, which are actively attempting to communicate with a bot server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>从具有可疑活动的 IP 地址登录
A risk detection triggered after a successful sign-in from an IP address with a high number of failed login attempts across multiple user accounts over a short period of time.

### <a name="sign-in-from-unfamiliar-location"></a>从不熟悉的位置登录
A risk detection triggered when a user successfully signs in from a new location (IP, Latitude/Longitude, and ASN).

### <a name="sign-in-risk"></a>登录风险
请参阅“风险级别（登录）”

### <a name="sign-in-risk-policy"></a>登录风险策略
A Conditional Access policy that evaluates the risk to a specific sign-in and applies mitigations based on predefined conditions and rules.

### <a name="user-compromise-risk"></a>用户泄漏风险
请参阅“风险级别（用户入侵）”

### <a name="user-risk"></a>用户风险
请参阅“风险级别（用户入侵）”。

### <a name="user-risk-policy"></a>用户风险策略
A Conditional Access policy that considers the sign-in and applies mitigations based on predefined conditions and rules.

### <a name="users-flagged-for-risk"></a>已标记为存在风险的用户
Users that have risk detections, which are either active or remediated

### <a name="vulnerability"></a>漏洞
Azure Active Directory 中导致目录容易遭到入侵或威胁的配置或状态。

## <a name="see-also"></a>另请参阅

- [Azure Active Directory 标识保护](../active-directory-identityprotection.md)
