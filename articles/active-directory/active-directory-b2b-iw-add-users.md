---

title: "信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？ | Microsoft 文档"
description: "Azure Active Directory B2B 协作允许信息工作者将其组织的用户添加到 Azure AD，以便其访问你的公司应用程序"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/02/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d9ffd4176e87b6b5ada882ff09f507665bda7b1d
ms.openlocfilehash: 7bad8269c3756979fe48d130a0cdfb02ceafad86


---

# <a name="how-do-information-workers-add-b2b-collaboration-users-to-azure-active-directory"></a>信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？

信息工作者可以使用[应用程序访问面板](http://myapps.microsoft.com)将 B2B 协作用户添加到组和他们作为管理员的应用程序。

## <a name="information-workers-adding-b2b-collaboration-users-to-an-application"></a>将 B2B 协作用户添加到应用程序的信息工作者
以合作伙伴组织中的信息工作者身份将 B2B 协作用户分配到应用，如以下视频中所示：

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps]

  如果此视频未显示嵌入，可以在[此处](https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps)访问它。

## <a name="information-workers-adding-b2b-collaboration-users-to-a-group"></a>将 B2B 协作用户添加到组的信息工作者

同样，信息工作者可以将 B2B 协作用户添加到已为自助服务组管理启用的分配组中。
> [!NOTE]
不能将 B2B 协作用户添加到动态组或已与本地 Active Directory 同步的组。

## <a name="add-without-invitation"></a>在未邀请的情况下添加

如果邀请者所属的角色在要从中添加用户的合作伙伴组织的目录中具有枚举权限，则受邀请的用户将添加到邀请方组织而无需邀请。

以下是此功能最有用的方案：
1. 主办单位（例如，WoodGrove）中的用户邀请合作伙伴组织中的一个用户（例如，Sam@litware.com)）作为来宾。
2. 主办单位的管理员设置的策略允许 Sam 标识并添加合作伙伴组织 (Litware) 中的其他用户。
4. 现在，Sam 可以将 Litware 中的其他用户添加到 WoodGrove 目录、组或应用程序而无需兑换邀请。 如果 Sam 在 Litware 中具有相应的枚举权限，这种操作将自动进行。


* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作故障排除](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


