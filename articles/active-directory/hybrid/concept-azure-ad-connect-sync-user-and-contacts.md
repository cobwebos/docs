---
title: Azure AD Connect 同步：了解用户、组和联系人 | Microsoft Docs
description: 介绍 Azure AD Connect 同步中的用户、组和联系人。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9470e9af38fdd814f5059538656e6a3dbb8e3a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279306"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect 同步：了解用户、组和联系人
有几个不同的原因导致你会有多个 Active Directory 林，并且还有几个不同的部署拓扑。 常见的模型包括合并和收购之后的帐户-资源部署和 GAL 同步的林。 但即使有纯模型，混合模型也是常见的模型。 Azure AD Connect 同步中的默认配置是不会假定任何特定模型，但观察到的行为可能会有所不同，这具体取决于安装指南中如何选择用户匹配。

本主题讨论默认配置在某些拓扑中的行为方式。 我们将讨论配置，并且同步规则编辑器可用于查看配置。

有几个配置假定的一般规则：
* 不管按什么顺序从源 Active Directory 导入，最终结果始终相同。
* 活动帐户会始终提供登录信息，包括 **userPrincipalName** 和 **sourceAnchor**。
* 如果找不到活动的帐户，已禁用帐户会提供 userPrincipalName 和 sourceAnchor，除非该帐户为已链接邮箱。
* 具有已链接邮箱的帐户永远不会用于 userPrincipalName 和 sourceAnchor。 据推测，更高版本中会找到有效帐户。
* 可能为 Azure AD 设置联系人对象，作为联系人或用户。 在处理完所有源 Active Directory 林之前，你不会知道是哪一种。

## <a name="groups"></a>组
将组从 Active Directory 同步到 Azure AD 时，注意的要点是：

* Azure AD Connect 会从目录同步中排除内置安全组。

* Azure AD Connect 不支持将[主要组成员身份](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11))同步到 Azure AD。

* Azure AD Connect 不支持将[动态分发组成员身份](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups?view=exchserver-2019)同步到 Azure AD。

* 若要以启用邮件的组的形式将 Active Directory 组同步到 Azure AD：

    * 如果该组的 *proxyAddress* 属性为空，则其 *mail* 属性必须包含一个值

    * 如果组的 *proxyAddress* 属性为非空，则必须至少包含一个 SMTP 代理地址值。 下面是一些示例：
    
      * 其 proxyAddress 属性包含值 *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* 的 Active Directory 组在 Azure AD 中不会启用邮件。 它没有 SMTP 地址。
      
      * 其 proxyAddress 属性包含值 *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe\@contoso.com"}* 的 Active Directory 组在 Azure AD 中会启用邮件。
      
      * 其 proxyAddress 属性包含值 *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe\@contoso.com"}* 的 Active Directory 组在 Azure AD 中也会启用邮件。

## <a name="contacts"></a>联系人
合并和收购之后，不同林中具有表示用户的联系人很常见，其中，GALSync 解决方案对两个或多个 Exchange 林桥接。 联系人对象始终使用邮件属性从连接器空间联接到 metaverse。 如果已存在具有相同邮件地址的联系人对象或用户对象，则会将这些对象联接在一起。 这是在规则 **In from AD – Contact Join** 中进行配置的。 此外还有一个名为 **In from AD – Contact Common** 的规则，使用流向 Metaverse 属性 **sourceObjectType** 的属性流，并使用常量 **Contact**。 此规则的优先级非常低，因此如果将任何用户对象联接到同一 Metaverse 对象，规则 **In from AD – User Common** 就会向该属性提供值“User”。 在使用此规则的情况下，如果没有联接任何用户，此属性则会具有值 Contact，如果至少找到了一个用户，则会具有值 User。

若要将对象预配到 Azure AD，如果元节属性**sourceObjectType**设置为 "**联系人**"，则出站规则**Out to AAD – Contact Join**将创建 Contact 对象。 如果将此属性设置为 **user**，则规则 **Out To AAD – user Join** 将改为创建用户对象。
当导入和同步更多源 Active Directory 时，对象很可能由 Contact 提升为 User。

例如，在 GALSync 拓扑中，当我们导入第一个林时，我们会在第二个林中发现每个的联系人对象。 这会在 AAD 连接器中暂存新的联系人对象。 当我们之后导入并同步第二个林时，我们会找到实际用户并将他们联接到现有的 metaverse 对象。 然后我们会删除 AAD 中的联系人对象，并改为创建新的用户对象。

如果有将用户表示为联系人的拓扑，请确保选择匹配安装指南中 mail 属性上的用户。 如果选择另一个选项，则会具有依赖于顺序的配置。 联系人对象始终会联接 mail 属性，但如果安装指南中选择了此选项，则用户对象只会联接 mail 属性。 如果在用户对象之前已导入联系人对象，那么具有相同 mail 属性的 metaverse 中可能最终会有两个不同的对象。 在导出到 Azure AD 期间，会引发错误。 此行为是设计使然，并且会指示错误数据或者在安装过程中未正确标识拓扑。

## <a name="disabled-accounts"></a>已禁用帐户
已禁用帐户也会同步到 Azure AD。 已禁用帐户在 Exchange 中表示资源是很常见的，例如会议室。 例外情况是具有已链接邮箱的用户；如前文所述，这些用户永远不会将帐户设置到 Azure AD。

假设是，如果找到已禁用的用户帐户，那么之后我们将找不到另一个有效帐户，并且在找到 userPrincipalName 和 sourceAnchor 的情况下，对象会设置到 Azure AD。 如果另一个有效帐户联接到相同的 metaverse 对象，则会使用其 userPrincipalName 和 sourceAnchor。

## <a name="changing-sourceanchor"></a>更改 sourceAnchor
当对象已导出到 Azure AD 时，则不再允许更改 sourceAnchor。 当已导出对象时，则采用 Azure AD 接受的 **sourceAnchor** 值设置 metaverse 属性 **cloudSourceAnchor**。 如果 **sourceAnchor** 在更改后与 **cloudSourceAnchor** 不匹配，则规则 **Out to AAD – User Join** 会引发错误 **“sourceAnchor 属性已更改”**。 在这种情况下，必须更正配置或数据，以便相同的 sourceAnchor 再次在 metaverse 中出现，才能再次同步对象。

## <a name="additional-resources"></a>其他资源
* [Azure AD Connect 同步：自定义同步选项](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)