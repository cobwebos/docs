---
title: "适用于 Azure Active Directory B2B 协作预览版的外部用户对象属性更改 | Microsoft 文档"
description: "Azure Active Directory B2B 可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 4ede9c7b-3830-42f2-b73d-cbecbcc7db64
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d57fd7d6dcd186d1e79528c716d8bf81ad868ae5


---
# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Azure AD B2B 协作预览版：外部用户对象属性更改
Azure AD 目录中的每个用户即为一个用户对象。 在 B2B 协作邀请兑换流的各个阶段，会对 Azure AD 中的用户对象属性进行更改。 合作伙伴用户单击邀请电子邮件中的链接时，表示目录中合作伙伴用户的用户对象中具有在兑换时会更改的属性。 具体而言：

* 会填充属性 **SignInName** 和 **AltSecId**
* **DisplayName** 属性从用户主体名称 (user_fabrikam.com#EXT#@contoso.com) 更改为登录名 (user@fabrikam.com)

在 Azure AD 中跟踪这些属性可以帮助你确定合作伙伴用户是否已兑换其 B2B 协作邀请。

## <a name="related-articles"></a>相关文章
在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [工作原理](active-directory-b2b-how-it-works.md)
* [详细演练](active-directory-b2b-detailed-walkthrough.md)
* [CSV 文件格式参考](active-directory-b2b-references-csv-file-format.md)
* [外部用户令牌格式](active-directory-b2b-references-external-user-token-format.md)
* [当前预览版限制](active-directory-b2b-current-preview-limitations.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


