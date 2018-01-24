---
title: "适用于混合组织的 Azure Active Directory B2B 协作 | Microsoft Docs"
description: "通过 Azure AD B2B 协作授予合作伙伴对本地资源和云资源的访问权限"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>适用于混合组织的 Azure Active Directory B2B 协作

在混合组织中同时存在本地资源和云资源，你可能希望授予外部合作伙伴对本地资源和云资源的访问权限。 对于对本地资源的访问权限，你可以在本地 Active Directory 环境中在本地管理合作伙伴帐户。 合作伙伴使用其合作伙伴帐户凭据登录来访问你的组织的资源。 若要使用同样的凭据向合作伙伴授予对云资源的访问权限，现在可以使用 Azure Active Directory (Azure AD) Connect 来将合作伙伴帐户作为 Azure AD B2B 用户（即 UserType = Guest 的用户）同步到云。

## <a name="identify-unique-attributes-for-usertype"></a>识别 UserType 的唯一属性

在启用 UserType 属性的同步之前，必须首先确定如何从本地 Active Directory 派生 UserType 属性。 换句话说，你的本地环境中的哪些参数对于外部协作者而言是唯一的？ 确定能够将这些外部协作者与你自己的组织中的成员进行区分的参数。

用于实现此目的的两种常用方法是：

- 指定一个未使用的本地 Active Directory 属性（例如 extensionAttribute1）来用作源属性。 
- 或者，从其他属性派生 UserType 属性的值。 例如，如果用户的本地 Active Directory UserPrincipalName 属性以域 *@partners.fabrikam123.org* 结尾，则你可能希望将所有用户同步为 Guest。
 
有关详细的属性要求，请参阅[启用 UserType 同步](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)。 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>配置 Azure AD Connect 来将用户同步到云

在识别唯一属性后，可以配置 Azure AD Connect 来将这些用户作为 Azure AD B2B 用户（即 UserType = Guest 的用户）同步到云。 从授权角度来看，这些用户与通过 Azure AD B2B 协作邀请流程创建的 B2B 用户没有区别。

有关实现说明，请参阅[启用 UserType 同步](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure AD B2B 协作的概述，请参阅[什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
- 有关 Azure AD Connect 的概述，请参阅[将本地目录与 Azure Active Directory 进行集成](connect/active-directory-aadconnect.md)。

