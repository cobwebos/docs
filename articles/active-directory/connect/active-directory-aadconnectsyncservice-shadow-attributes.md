---
title: "Azure AD Connect 同步服务影子属性 | Microsoft Docs"
description: "介绍影子属性在 Azure AD Connect 同步服务中的工作方式。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 4963888748d7103e3b24ac9c8de3d10ef9554fd4
ms.lasthandoff: 03/18/2017


---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect 同步服务影子属性
大多数属性在 Azure AD 中的表示方式与在本地 Active Directory 中相同。 但某些属性有一些特殊处理，并且 Azure AD 中的属性值可能不同于 Azure AD Connect 同步的值。

## <a name="introducing-shadow-attributes"></a>引入了影子属性
某些属性在 Azure AD 中有两种表示形式。 将存储本地值和计算值。 这些额外的属性称为影子属性。 表示此行为的两个最常用属性是 **userPrincipalName** 和 **proxyAddress**。 当这些属性中有表示非已验证域的值时，属性值将发生更改。 但 Connect 中的同步引擎可读取影子属性的值，因此从其角度来看，该属性已由 Azure AD 确认。

无法使用 Azure 门户或 PowerShell 查看影子属性。 但对于属性在本地和云中具有不同值的某些方案，了解此概念可帮助对这些方案进行故障排除。

若要更好地了解此行为，请查看 Fabrikam 中的此示例：  
![域](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
这些域在其本地 Active Directory 中有多个 UPN 后缀，但只验证了其中一个。

### <a name="userprincipalname"></a>userPrincipalName
用户在非已验证域中具有以下属性值：

| 属性 | 值 |
| --- | --- |
| 本地 userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

userPrincipalName 属性是在使用 PowerShell 时显示的值。

由于实际本地属性值存储在 Azure AD 中，因此当你验证 fabrikam.com 域时，Azure AD 使用 shadowUserPrincipalName 的值更新 userPrincipalName 属性。 对于要更新的这些值，无需从 Azure AD Connect 同步任何更改。

### <a name="proxyaddresses"></a>proxyAddresses
用于仅包括已验证域的相同过程也会对 proxyAddresses 执行，但会使用一些其他逻辑。 仅对邮箱用户进行已验证域检查。 已启用邮件的用户或联系人表示另一个 Exchange 组织中的用户，只能将 proxyAddresses 中的任何值添加到这些对象。

对于邮箱用户（无论是在本地还是在 Exchange Online 中），将仅显示已验证域的值。 它可能如下所示：

| 属性 | 值 |
| --- | --- |
| 本地 proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

在这种情况下，**smtp:abbie.spencer@fabrikam.com** 已删除，因为尚未验证该域。 但 Exchange 还添加了 **SIP:abbie.spencer@fabrikamonline.com**。 Fabrikam 未使用本地 Lync/Skype，但 Azure AD 和 Exchange Online 准备使用它。

proxyAddresses 的此逻辑称为 **ProxyCalc**。 在以下情况下，每次更改用户时，将调用 ProxyCalc：

- 为用户分配了包含 Exchange Online 的服务计划，即使未授权该用户使用 Exchange，也是如此。 例如，如果为用户分配了 Office E3 SKU，但仅为其分配了 SharePoint Online。 即使邮箱仍在本地，也是如此。
- 属性 msExchRecipientTypeDetails 具有值。
- 更改 proxyAddresses 或 userPrincipalName。

ProxyCalc 可能需要一些时间来处理用户更改，并且不会与 Azure AD Connect 导出过程同步。

> [!NOTE]
> 对于本主题中未介绍的高级方案，ProxyCalc 逻辑具有一些其他行为。 本主题供你了解行为并未记录所有内部逻辑。

### <a name="quarantined-attribute-values"></a>隔离的属性值
有重复的属性值时，也使用影子属性。 有关详细信息，请参阅[重复属性复原](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)。

## <a name="see-also"></a>另请参阅
* [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。

