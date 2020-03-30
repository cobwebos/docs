---
title: Azure 活动目录风险检测 |微软文档
description: 本艺术为您提供了风险检测的详细概述。
services: active-directory
keywords: azure active directory 标识保护, 安全性, 风险, 风险级别, 漏洞, 安全策略
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383950"
---
# <a name="azure-active-directory-risk-detections"></a>Azure 活动目录风险检测

大多数安全违规出现在当攻击者通过窃取用户的标识来获取环境的访问权限时。 发现标识是否遭到入侵并不容易。 Azure Active Directory 使用自适应机器学习算法和试探法来检测与用户帐户相关的可疑操作。 每个检测到的可疑操作都存储在称为**风险检测**的记录中。

在两个位置，您可以查看报告的风险检测：

 - **Azure AD 报告**- 风险检测是 Azure AD 安全报告的一部分。 有关详细信息，请参阅[风险用户安全报告](concept-user-at-risk.md)和[有风险的登录安全报告](concept-risky-sign-ins.md)。

 - **Azure AD 标识保护**- 风险检测也是[Azure 活动目录标识保护](../active-directory-identityprotection.md)的报告功能的一部分。

此外，您可以使用[身份保护风险检测 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)使用 Microsoft 图形获得对安全检测的编程访问。 有关详细信息，请参阅 [Azure Active Directory 标识保护和 Microsoft Graph 入门](../identity-protection/graph-get-started.md)。 

目前，Azure 活动目录检测六种类型的风险检测：

- [具有已泄漏凭据的用户](#leaked-credentials) 
- [从匿名 IP 地址登录](#sign-ins-from-anonymous-ip-addresses) 
- [不可能前往异常位置](#impossible-travel-to-atypical-locations) 
- [从受感染的设备登录](#sign-ins-from-infected-devices) 
- [从具有可疑活动的 IP 地址登录](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [从不熟悉的位置登录](#sign-in-from-unfamiliar-locations) 

![风险检测](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> 有时，您可能会在[登录报告中](concept-sign-ins.md)发现没有相应登录条目的风险检测。 这是因为“标识保护”评估“交互式”和“非交互式”登录的风险，而登录报表只显示交互式登录********。

检测到的风险检测的见解与 Azure AD 订阅相关联。 

* 使用**Azure AD 高级 P2 版本**，您可以获取有关所有基础检测的最详细信息。 
* 使用**Azure AD 高级 P1 版本**时，高级检测（如不熟悉的登录属性）不在许可证范围内，并将以登录名称显示，**并检测到其他风险**。 此外，风险级别和风险详细信息字段被隐藏。

虽然检测风险检测已经是保护身份的一个重要方面，但您也可以选择手动解决这些问题或通过配置条件访问策略实现自动响应。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。

## <a name="risk-detection-types"></a>风险检测类型

**风险检测类型**属性是已为其创建风险检测记录的可疑操作的标识符。

Microsoft 对检测过程的持续投入使得：

- 提高现有风险检测的检测精度 
- 将来将添加的新风险检测类型

### <a name="leaked-credentials"></a>已泄漏凭据

当网络犯罪分子泄露合法用户的有效密码时，他们通常会共享这些凭据。 共享方式通常为将凭据公开发布在暗网或粘贴网站上，或者在黑市上交易或出售凭据。 Microsoft 泄露凭据服务通过监控公网和暗网并与以下机构合作获取用户名/密码对：

- 研究人员
- 执法机构
- Microsoft 安全团队
- 其他受信任的来源 

当服务获取用户名/密码对时，将根据 AAD 用户当前的有效凭据进行检查。 找到匹配项时，意味着用户的密码已泄露，并创建**泄露的凭据风险检测**。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>从匿名 IP 地址登录

此风险检测类型标识从已标识为匿名代理 IP 地址的 IP 地址成功登录的用户。 这些代理通常是想要隐藏其设备 IP 地址的用户才会使用，他们可能意图不良。

### <a name="impossible-travel-to-atypical-locations"></a>不可能前往异常位置

此风险检测类型可标识从相距遥远的地理位置进行的两次登录，根据用户以往的行为，其中至少有一个位置属于异常。 除了若干其他因素外，此机器学习算法还考虑两次登录之间相隔的时间以及用户从第一个位置前往第二个位置所需的时间，因为这指示有不同的用户在使用相同的凭据。

此算法会忽略明显的“误报”，从而改善不可能前往条件，例如组织中其他用户定期使用的 VPN 和位置。 系统具有 14 天的初始学习期限，在此期间它将学习新用户的登录行为。 

### <a name="sign-in-from-unfamiliar-locations"></a>从不熟悉的位置登录

此风险检测类型考虑过去的登录位置（IP、纬度/经度和 ASN），以确定新的/不熟悉的位置。 系统会存储用户以前的登录位置信息，并将其视为“熟悉”位置。 当从尚未在熟悉位置列表中列出的位置登录时，将触发此风险检测。 系统具有 30 天的初始学习期间，在此期间它不会将任何新位置标记为不熟悉的位置。 系统还会忽略从常用设备和接近熟悉位置的地理位置进行登录。 

“标识保护”检测来自不熟悉位置的登录，也用于基本身份验证/旧版协议。 由于这些协议没有熟悉的新型特性（如客户端 ID），因此没有足够的遥测数据来减少误报。 要减少检测到的风险检测数量，应转到现代身份验证。   

> [!NOTE]
> 如果登录用户名和密码不匹配，登录将失败，并且不会发生风险检测。 仅成功登录时才会触发来自不熟悉的位置风险检测的登录。

### <a name="sign-ins-from-infected-devices"></a>从受感染的设备登录

此风险检测类型标识受恶意软件感染的设备的登录，已知这些设备会主动与机器人服务器通信。 这通过分析用户设备的 IP 地址与连接过僵尸服务器的 IP 地址之间的相互关系可得以确定。 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>从具有可疑活动的 IP 地址登录
此风险检测类型标识 IP 地址，这些地址在短时间内在多个用户帐户之间看到大量失败的登录尝试。 这与攻击者使用的 IP 地址的流量模式相似，在很大程度上表明帐户已遭到入侵或即将遭到入侵。 这是可忽略明显的误报的机器学习算法，例如通常由组织中其他用户使用的 IP 地址。  系统具有 14 天的初始学习期限，在此期间它将学习新用户和新租户的登录行为。

## <a name="detection-type"></a>检测类型

检测类型属性是风险检测检测时间帧的指示器（**实时**或**脱机**）。 目前，大多数风险检测在风险检测发生后在后处理操作中处于脱机状态。

下表列出了不同检测类型显示在相关报表中时所用的时间：

| 检测类型 | 报告延迟 |
| --- | --- |
| 实时 | 5 - 10 分钟 |
| Offline | 2 - 4 小时 |


对于 Azure 活动目录检测到的风险检测类型，检测类型为：

| 风险检测类型 | 检测类型 |
| :-- | --- | 
| [具有已泄漏凭据的用户](#leaked-credentials) | Offline |
| [从匿名 IP 地址登录](#sign-ins-from-anonymous-ip-addresses) | 实时 |
| [不可能前往异常位置](#impossible-travel-to-atypical-locations) | Offline |
| [从不熟悉的位置登录](#sign-in-from-unfamiliar-locations) | 实时 |
| [从受感染的设备登录](#sign-ins-from-infected-devices) | Offline |
| [从具有可疑活动的 IP 地址登录](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>风险级别

风险检测的风险级别属性是风险检测严重性和信任度（**高**、**中**或**低**）的指标。 此属性可帮助确定必须执行的操作的优先级。 

风险检测的严重性表示信号的强度，作为身份泄露的预测器。 置信度是误报可能性的指示器。 

例如， 

* **高**： 高置信度和高严重性风险检测。 这些事件指出用户的标识明显已遭入侵，应立即补救所有受影响的用户帐户。

* **中等**： 严重性高，但置信风险检测较低，反之亦然。 这些事件具有潜在风险，应补救所有受影响的用户帐户。

* **低**： 低置信度和低严重性风险检测。 此事件可能不需要立即操作，但当与其他风险检测结合使用时，可能会提供标识受损的强烈指示。

![风险级别](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>已泄漏凭据

泄露的凭据风险检测被归类为**高**，因为它们提供了一个明确的指示，表明攻击者可以使用用户名和密码。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>从匿名 IP 地址登录

此风险检测类型的风险级别为 **"中等"，** 因为匿名 IP 地址不是帐户泄露的有力指示。 我们建议立即联系用户，验证他们是否使用了匿名 IP 地址。


### <a name="impossible-travel-to-atypical-locations"></a>不可能前往异常位置

不可能前往通常是一个出色的指示器，可指示黑客已能够成功登录。 但是，当用户使用新设备或使用组织中其他用户通常不使用的 VPN 前往时，可能会发生误报。 容易产生误报的另一个源头是误将服务器 IP 作为客户端 IP 传递的应用程序，这可能会让登录看起来像是从托管应用程序后端的数据中心发起的（这些数据中心通常是 Microsoft 数据中心，所以登录看起来像是从 Microsoft 拥有的 IP 地址发起的）。 由于这些误报，此风险检测的风险级别为**中等**。

> [!TIP]
> 您可以通过配置[命名位置](../active-directory-named-locations.md)来减少此风险检测类型的报告误报量。 

### <a name="sign-in-from-unfamiliar-locations"></a>从不熟悉的位置登录

不熟悉的位置可明显指示攻击者能够使用被盗的标识。 当用户旅行，试用新设备或使用新 VPN 时，可能发生误报。 由于这些误报，此事件类型的风险级别为 **"中**"。

### <a name="sign-ins-from-infected-devices"></a>从受感染的设备登录

此风险检测标识 IP 地址，而不是用户设备。 如果多个设备位于单个 IP 地址后面，并且只有一些设备由自动程序网络控制，则从其他设备登录我不必要地触发此事件，这就是为什么此风险检测被归类为**Low**。  

我们建议联系用户并扫描用户的所有设备。 用户的个人设备也可能受感染，其他人也可能以用户身份从相同 IP 地址使用受感染的设备。 这些设备之所以受感染，通常是因为下载了防病毒软件尚未识别的恶意软件，也可能是因为用户存在不良习惯，导致设备容易遭受感染。

有关如何解决恶意软件感染的详细信息，请参阅[恶意软件防护中心](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)。

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>从具有可疑活动的 IP 地址登录

我们建议联系用户，验证他们实际上是否已从标记为可疑的 IP 地址登录。 这一事件类型的风险级别是“中等”****，因为可能有多台设备在使用同一 IP 地址，但其中只有部分设备可能执行了可疑活动。 


## <a name="next-steps"></a>后续步骤

* [“有风险的用户”安全报告](concept-user-at-risk.md)
* [“有风险的登录”安全报告](concept-risky-sign-ins.md)
* [Azure AD 标识保护](../active-directory-identityprotection.md)。
