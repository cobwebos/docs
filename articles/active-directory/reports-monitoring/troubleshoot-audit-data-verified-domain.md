---
title: 对已验证的域更改的审核数据进行故障排除 | Microsoft Docs
description: 提供在更改用户验证的域时将在 Azure Active Directory 活动日志中显示的信息。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87117423"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>疑难解答：针对已验证的域更改审核数据 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>我发现大量用户更改，但不确定是什么原因造成的。

### <a name="symptoms"></a>症状

我检查了 Azure AD 审核日志，并发现 Azure AD 租户中有多个用户更新。 这些“更新用户”事件不会显示“行动者”信息，因此这会导致不确定是谁触发了大规模用户更改 。 

### <a name="cause"></a>原因

 大规模对象更改背后的一个常见原因是称为“ProxyCalc”的非同步后端操作。  “ProxyCalc”是一个逻辑，用于确定适当的“UserPrincipalName”和“Proxy Addresses”，并在 Azure AD 用户、组或联系人信息中进行更新  。 “ProxyCalc”的设计旨在确保“Azure AD”中所有的“UserPrincipalName”和“Proxy Addresses”任何时候都是一致的  。 “ProxyCalc”必须由显式更改（例如已验证的域更改）触发，并且不能永久作为任务在后台运行。 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>UserPrincipalName 一致性意味着什么？ 

对于仅限云的用户，一致性意味“UserPrincipalName”设置为已验证的域后缀。 处理不一致的 **UserPrincipalName** 时， **ProxyCalc** 会将其转换为默认的 onmicrosoft.com 后缀，例如： username@Contoso.onmicrosoft.com 

对于同步的用户，一致性意味着“UserPrincipalName”设置为已验证的域后缀，并与本地“UserPrincipalName”值 (ShadowUserPrincipalName) 匹配 。 处理不一致的 **UserPrincipalName** 时， **ProxyCalc** 将恢复为与 **ShadowUserPrincipalName** 相同的值; 或者，如果已从租户中删除域后缀，则会将其转换为默认的 *. onmicrosoft.com 域后缀。 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Proxy Address 一致性意味着什么？ 

对于仅限云的用户，一致性意味着 Proxy Addresses 与已验证的域后缀相匹配。 当处理不一致的代理地址时， **ProxyCalc** 会将其转换为默认的 *. onmicrosoft.com 域后缀，例如： SMTP:username@Contoso.onmicrosoft.com 

对于同步的用户，一致性意味着 Proxy Address 与（一个或多个）本地 Proxy Address 值相匹配（即 ShadowProxyAddresses）。 “ProxyAddresses”应与“ShadowProxyAddresses”同步 。 如果同步的用户分配了 Exchange 许可证，则代理地址必须与（一个或多个）本地代理地址值相匹配，并且还必须与已验证的域后缀相匹配。 在此方案中，“ProxyCalc”将使用未验证的域后缀净化不一致的代理地址，并从 Azure AD 中的对象中删除。 如果该未验证的域稍后进行了验证，则“ProxyCalc”将重新计算，并会将代理地址从“ShadowProxyAddresses”添加回 Azure AD 中的对象 。  

> [!NOTE]
> 对于同步的对象，若要避免“ProxyCalc”逻辑计算出意外的结果，最好将代理地址设置为本地对象上的 Azure AD 验证域。  

  
有一个管理任务会在每当发生经过验证的域更改时，触发“ProxyCalc”。 每次从 Azure AD 租户中添加/删除一个已验证的域时，都会发生此任务，会在内部触发“ProxyCalc”。  

例如，如果将已验证的域 Fabrikam.com 添加到 Contoso.onmicrosoft.com 租户，此操作将触发租户中所有对象的 ProxyCalc 操作。 Azure AD 审核日志中将此事件捕获为“更新用户”事件，其前面是“添加已验证的域”事件 。 另一方面，如果 Fabrikam.com 已从 Contoso.onmicrosoft.com 租户中删除，则所有 **更新用户** 事件的前面都将有 " **删除已验证的域** " 事件。   

#### <a name="additional-notes"></a>其他说明：

ProxyCalc 不会导致符合以下条件的对象发生变化： 

- 没有有效 Exchange 许可证 
- 将“MSExchRemoteRecipientType”设置为 Null 
- 不被视为共享资源。 共享资源是指当“CloudMSExchRecipientDisplayType”包含以下值之一的情况：“MailboxUser (shared)”、“PublicFolder”、“ConferenceRoomMailbox”、“EquipmentMailbox”、“ArbitrationMailbox”、“RoomList”、“TeamMailboxUser”、“Group mailbox”、“Scheduling mailbox”、“ACLableMailboxUser”、“ACLableTeamMailboxUser”           
  
 为了在这两个不同的事件之间建立更多的关联，Microsoft 正在努力更新审核日志中的“行动者”信息，以将这些更改识别为由已验证的域更改触发的。 此操作将帮助检查已验证的域更改事件的发生时间，并开始大规模更新其租户中的对象。 

此外，在大多数情况下，没有任何用户更改，因为其“UserPrincipalName”和“Proxy Addresses”是一致的，因此，我们只在审核日志中显示那些导致对象实际更改的更新 。 此操作将阻止审核日志中出现干扰，并帮助管理员将其余用户更改与已验证的域更改事件进行关联，如上文所述。 

## <a name="next-steps"></a>后续步骤

[Azure AD Connect 同步服务影子属性](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
