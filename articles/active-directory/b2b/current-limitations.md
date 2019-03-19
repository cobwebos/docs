---
title: B2B 协作-Azure Active Directory 的限制 |Microsoft Docs
description: Azure Active Directory B2B 协作的当前限制
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bf4d1d0f510ccad614452db74c291f6c61dcf54
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672298"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure Active Directory B2B 协作的限制
Azure Active Directory (Azure AD) B2B 协作当前具有本文中描述的限制。

## <a name="possible-double-multi-factor-authentication"></a>可能需要进行两次多重身份验证
使用 Azure AD B2B，可在资源组织（邀请方组织）上进行多重身份验证。 [B2B 协作用户的条件访问](conditional-access.md)中对使用此方法的原因进行了详细说明。 如果合作伙伴已设置并实施了多重身份验证，则他们的用户可能必须在其主组织中执行一次身份验证，然后在你的组织中再次进行身份验证。

## <a name="instant-on"></a>即时启用
通过 B2B 协作流，我们将用户添加到目录，并在邀请兑换、应用分配等期间动态更新用户。 更新和写入通常发生在一个目录实例中，并且必须复制到所有实例中。 更新所有实例后完成复制。 有时，如果在一个实例中编写或更新对象，但是检索该对象的调用针对的是另一个实例，就会出现复制延迟。 如果发生这种情况，刷新或重试可有所帮助。 如果正在使用 API 编写应用，则请重试后退操作，这是解决该问题的一个很好的防御措施。

## <a name="azure-ad-directories"></a>Azure AD 目录
Azure AD B2B 受制于 Azure AD 服务目录限制。 有关用户可以创建的目录数以及用户或来宾用户可以属于的目录数的详细信息，请参阅 [Azure AD 服务限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [委托 B2B 协作邀请](delegate-invitations.md)

