---
title: 排查验证的域更改的审核数据问题 |Microsoft Docs
description: 提供更改用户验证的域时在 Azure Active Directory 活动日志中显示的信息。
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
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117423"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>故障排除：审核验证域更改时的数据 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>我对我的用户进行了大量更改，但不确定其原因。

### <a name="symptoms"></a>症状

我检查 Azure AD 审核日志，并查看在我的 Azure AD 租户中发生的多个用户更新。 这些**更新用户**事件**不显示执行**组件信息，这会导致对用户触发大量更改的人员的不确定性。 

### <a name="cause"></a>原因

 大容量对象更改的一个常见原因是名为**ProxyCalc**的非同步后端操作。  **ProxyCalc**是确定在 Azure AD 用户、组或联系人中更新的适当**UserPrincipalName**和**代理地址**的逻辑。 **ProxyCalc**背后的设计旨在确保所有**UserPrincipalName**和**代理地址**在任何时候都保持 Azure AD 一致。 **ProxyCalc**必须由显式更改（如已验证的域更改）触发，而不会永久作为任务在后台运行。 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>UserPrincipalName 一致性意味着什么？ 

对于仅限云的用户，一致性意味着**UserPrincipalName**设置为已验证的域后缀。 处理不一致的**UserPrincipalName**时， **ProxyCalc**会将其转换为默认的 onmicrosoft.com 后缀，例如：username@Contoso.onmicrosoft.com 

对于同步用户，一致性意味着**UserPrincipalName**设置为已验证的域后缀，并与本地**UserPrincipalName**值（ShadowUserPrincipalName）相匹配。 处理不一致的**UserPrincipalName**时， **ProxyCalc**将恢复为与**ShadowUserPrincipalName**相同的值; 或者，如果已从租户中删除域后缀，则会将其转换为默认的 *. onmicrosoft.com 域后缀。 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>代理地址的一致性是什么意思？ 

对于仅限云的用户，一致性意味着代理地址匹配已验证的域后缀。 当处理不一致的代理地址时， **ProxyCalc**会将其转换为默认的 *. onmicrosoft.com 域后缀，例如：SMTP:username@Contoso.onmicrosoft.com 

对于同步用户，一致性意味着代理地址与本地代理地址（es）值匹配（即 ShadowProxyAddresses). **ProxyAddresses**应与**ShadowProxyAddresses**同步。 如果已同步用户分配了 Exchange 许可证，则代理地址必须与本地代理地址值匹配，并且还必须与已验证的域后缀匹配。 在这种情况下， **ProxyCalc**将使用未验证的域后缀净化不一致的代理地址，并将从 Azure AD 中的对象中删除。 如果以后验证该未验证的域， **ProxyCalc**将重新计算**ShadowProxyAddresses**中的代理地址并将其添加回 Azure AD 中的对象。  

> [!NOTE]
> 对于同步对象，若要避免**ProxyCalc**逻辑计算意外的结果，最好将代理地址设置为本地对象上的 Azure AD 验证域。  

  
每当发生已验证的域更改时，可能会触发**ProxyCalc**的管理任务之一。 每当在内部触发**ProxyCalc**的 Azure AD 租户中添加/删除已验证的域时，就会执行此任务。  

例如，如果将已验证的域 Fabrikam.com 添加到 Contoso.onmicrosoft.com 租户，此操作将触发租户中所有对象的 ProxyCalc 操作。 此事件将在 Azure AD 审核日志中捕获，作为**更新用户**事件，并在其中**添加已验证的域**事件。 另一方面，如果 Fabrikam.com 已从 Contoso.onmicrosoft.com 租户中删除，则所有**更新用户**事件的前面都将有 "**删除已验证的域**" 事件。   

#### <a name="additional-notes"></a>其他说明：

ProxyCalc 不会导致某些对象发生更改： 

- 没有活动的 Exchange 许可证 
- 已将**MSExchRemoteRecipientType**设置为 Null 
- 不被视为共享资源。 共享资源是指**CloudMSExchRecipientDisplayType**包含以下值之一： **MailboxUser （shared）**、 **PublicFolder**、 **ConferenceRoomMailbox**、 **EquipmentMailbox**、 **ArbitrationMailbox**、 **RoomList**、 **TeamMailboxUser**、 **Group 邮箱**、**计划邮箱**、 **ACLableMailboxUser**、 **ACLableTeamMailboxUser** 
  
 为了更好地在这两个不同的事件之间建立相关性，Microsoft 正致力于更新审核日志中的执行组件信息，以确定经过验证的域更改**触发的这些**更改。 此操作将帮助检查验证的域更改事件发生的时间，并开始对其租户中的对象进行大容量更新。 

此外，在大多数情况下，如果用户的**UserPrincipalName**和**代理地址**不一致，则不会对用户进行任何更改，因此，我们正在仅在审核日志中显示导致对对象的实际更改的那些更新。 此操作将阻止审核日志中出现干扰，并帮助管理员将其余用户更改与验证的域更改事件相关联，如上文所述。 

## <a name="next-steps"></a>后续步骤

[Azure AD Connect 同步服务影子属性](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
