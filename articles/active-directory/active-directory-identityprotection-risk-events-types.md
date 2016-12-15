---
title: "Azure Active Directory Identity Protection 检测到的风险事件类型 | Microsoft 文档"
description: "本主题详细概述了 Azure Active Directory Identity Protection 中的可用风险事件类型"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d39c5c7c-a4e5-459d-b78f-cbe31a46bb2f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fd1fb445a78d12046d52cb27798eaa9d058629c3


---
# <a name="types-of-risk-events-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection 检测到的风险事件类型
在 Azure Active Directory Identity Protection 中，风险事件是如下事件：

* 已标记为可疑
* 指示某个标识可能受到威胁。 

本主题详细概述了可用风险事件类型。

## <a name="leaked-credentials"></a>已泄漏凭据
Microsoft 安全研究人员发现了在暗网中公开发布的已泄漏凭据。 这些凭据通常以纯文本形式出现。 针对 Azure AD 凭据检查它们，如果存在匹配项，它们将在 Identity Protection 中报告为“已泄漏凭据”。

已泄漏凭据事件归类为“高”严重性风险事件，因为它们明确表示向攻击者提供用户名和密码。

## <a name="impossible-travel-to-atypical-locations"></a>不可能前往异常位置
此风险事件类型可标识从地理上较远位置的两次登录，根据用户以往的行为，其中至少还有一个位置属于异常。 此外，两次登录之间的时间短于用户从第一个位置前往第二个位置所花费的时间，这表明不同用户使用的是相同凭据。 

忽略明显“误报”的此机器学习算法可促进不可能前往条件，例如由组织中其他用户定期使用的 VPN 和位置。  系统具有 14 天的初始学习期限，在此期间它将学习新用户的登录行为。

不可能前往通常是一个出色的指示器，可指示黑客已能够成功登录。 但是，当用户使用新设备或使用组织中其他用户通常不使用的 VPN 前往时，可能会发生误报。 误报的另一个来源是将服务器 IP 错误传递为客户端 IP 的应用程序，可能看起来像从托管应用程序后端的数据中心中发生的登录（这些数据中心通常是 Microsoft 数据中心，可能看起来像从 Microsoft 所拥有的 IP 地址发生的登录）。 由于这些误报，此风险事件的风险级别为“中等”。

## <a name="sign-ins-from-infected-devices"></a>从受感染的设备登录
此风险事件类型可标识从感染了恶意软件的设备（即主动与僵尸服务器通信）登录。 这由针对与僵尸服务器连接的 IP 地址相关的用户设备 IP 地址确定。 

此风险事件可标识 IP 地址，而非用户设备。 如果单个 IP 地址后有多台设备，并且某些设备由僵尸网络控制，从其他设备登录可能会不必要地触发此事件，这就是将此任务事件分类为“低”的原因。  

我们建议你联系用户并扫描用户的所有设备。 用户的个人设备也可能受感染，或者如前面所述，其他人也可能以用户身份从相同 IP 地址使用受感染的设备。 受感染的设备通常受防病毒软件尚未标识的恶意软件感染，还可能表示已导致设备受感染的错误用户习惯。

有关如何解决恶意软件感染的详细信息，请参阅[恶意软件防护中心](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409)。

## <a name="sign-ins-from-anonymous-ip-addresses"></a>从匿名 IP 地址登录
此风险事件类型可标识已从标识为匿名代理 IP 地址的 IP 地址成功登录的用户。 这些代理由想要隐藏其设备的 IP 地址的用户使用，并且可能用于恶意目的。

我们建议你立即联系用户，验证他们是否使用了匿名 IP 地址。 此风险事件类型的风险级别为“中等”，因为本身使用匿名 IP 地址无法明显指示帐户受到威胁。

## <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>从具有可疑活动的 IP 地址登录
此风险事件类型可标识在较短时间内在多个用户帐户上发现大量失败登录尝试的 IP 地址。 这与攻击者使用的 IP 地址的前往模式相匹配，并且是指示帐户已受威胁或即将受威胁的明显指示器。 这是可忽略明显“误报”的机器学习算法，例如通常由组织中其他用户使用的 IP 地址。  系统具有 14 天的初始学习期限，在此期间它将学习新用户和新租户的登录行为。

我们建议你联系用户，验证他们实际上是否已从标记为可疑的 IP 地址登录。 此事件类型的风险级别是“中等”，因为同一 IP 地址后可能有多台设备，但仅某些设备可能导致可疑活动。 

## <a name="sign-in-from-unfamiliar-locations"></a>从不熟悉的位置登录
此风险事件类型是视为过去登录位置（IP、纬度/经度和 ASN）的实时登录评估机制，可确定新的/不熟悉的位置。 系统存储有关用户使用的之前位置的信息，并将这些位置视为“熟悉”位置。 当从尚未在熟悉位置列表中列出的位置登录时，将触发此风险事件。 系统具有 14 天的初始学习期限，在此期间不会将任何新位置标记为不熟悉的位置。 系统还将忽略从熟悉设备和在地理上接近熟悉位置的位置登录。 <br>
不熟悉的位置可明显指示攻击者能够尝试使用被盗的标识。 当用户前往时可能发生误报，试用新设备或使用新 VPN。 由于这些误报，此事件类型的风险级别为“中等”。

## <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 异常活动报告
其中的某些风险事件已通过 Azure 门户中的“Azure AD 异常活动报告”提供。 下表列出了各种风险事件类型和相应的“Azure AD 异常活动”报告。 Microsoft 持续在此领域投资，计划持续改进现有风险事件的检测准确性，不断添加新的风险事件类型。 

| Identity Protection 风险事件类型 | 相应的 Azure AD 异常活动报告 |
|:--- |:--- |
| 已泄漏凭据 |具有已泄漏凭据的用户 |
| 不可能前往异常位置 |异常登录活动 |
| 从受感染的设备登录 |从可能受感染的设备登录 |
| 从匿名 IP 地址登录 |从未知源登录 |
| 从具有可疑活动的 IP 地址登录 |从具有可疑活动的 IP 地址登录 |
| 从不熟悉的位置登录 |- |
| 锁定事件 |- |

以下 Azure AD 异常活动报告未作为风险事件包含在 Azure AD Identity Protection 中，因此不会通过 Identity Protection 提供。 这些报告仍在 Azure 门户中提供，但未来的某个时间它们将会过时，因为 Identity Protection 中的风险事件会将其取代。

* 多次失败后登录
* 从多个地理区域登录

## <a name="see-also"></a>另请参阅
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)




<!--HONumber=Nov16_HO3-->


