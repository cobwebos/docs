---
title: 共享链接，请求 Azure AD 授权管理中的访问包-Azure Active Directory
description: 了解如何共享链接，以在 Azure Active Directory 权利管理中请求访问包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968764"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>共享链接，以在 Azure AD 权利管理中请求访问包

目录中的大多数用户都可以登录到 "我的访问门户" 并自动查看他们可以请求的访问包的列表。 但是，对于尚未在你的目录中的外部业务合作伙伴用户，你将需要向他们发送一个可用于请求访问包的链接。 

只要为[外部用户启用](entitlement-management-catalog-create.md)了访问包的目录，并且你具有[外部用户目录的策略](entitlement-management-access-package-request-policy.md)，外部用户便可以使用 "我的访问门户" 链接来请求访问包。

## <a name="share-link-to-request-an-access-package"></a>共享用于请求访问包的链接

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 在 "概述" 页上，复制 "**我的 Access 门户" 链接**。

    ![访问包概述 - 我的访问权限门户链接](./media/entitlement-management-shared/my-access-portal-link.png)

    将整个 "我的访问门户" 链接发送到内部业务合作伙伴时，必须将其复制到一起，这一点很重要。 这可确保合作伙伴能够访问你的目录门户来发出请求。 该链接从 `myaccess`开始，包括一个目录提示，并以访问包 ID 结尾。  （对于美国政府版，将 `myaccess.microsoft.us`"我的 Access 门户" 链接中的域。）

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 发送电子邮件或发送到外部业务合作伙伴的链接。 它们可以与其用户共享链接来请求访问包。

## <a name="next-steps"></a>后续步骤

- [请求访问访问包](entitlement-management-request-access.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)