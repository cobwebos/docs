---
title: "Azure AD B2B 协作预览版：工作原理 | Microsoft 文档"
description: "说明 Azure Active Directory B2B 协作如何可让业务合作伙伴有选择性地访问本方的企业应用程序，从而为跨公司合作关系提供支持"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 0c1cdde6-1988-4eff-a876-75c8f845f0ed
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4f458a0835951692fa447d19b2ce1dda7cc2c8e2


---
# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Azure AD B2B 协作预览版：工作原理
Azure AD B2B 协作基于邀请，并且可兑换模型。 你提供你想要合作的相关方的电子邮件地址，以及你想让他们使用的应用程序。 Azure AD 向他们发送电子邮件邀请，其中包含一个链接。 合作伙伴用户打开该链接，系统将提示他使用其 Azure AD 帐户登录或注册新的 Azure AD 帐户。

1. 你的管理员通过使用 Azure 门户上载[结构化 .csv 文件](active-directory-b2b-references-csv-file-format.md)来邀请合作伙伴用户 。
2. 门户向这些合作伙伴用户发送邀请电子邮件。
3. 合作伙伴用户单击电子邮件中的链接，系统将提示他们使用其工作凭据登录（如果他们已在 Azure AD 中）或注册为 Azure AD B2B 协作用户。
4. 合作伙伴用户将被重定向到邀请他们使用的应用程序，现在他们已对其具有访问权限。

## <a name="directory-operations"></a>目录操作
合作伙伴用户作为外部用户存在于 Azure AD 中。 这意味着管理员可以通过 Azure 门户或使用 Azure PowerShell 预配许可证、分配组成员身份，并进一步授予对公司应用的访问权限，就像对公司中的用户执行操作一样。

虽然付费 Azure AD 订阅（基本或高级）不是使用 Azure AD B2B 的必需条件，但是拥有付费 Azure AD 订阅（基本或高级）的租户可以收到以下附加好处：

* 管理员可以将组分配到应用，使得管理受邀用户访问更为简单。
* 管理员租户品牌用于设置邀请电子邮件和兑换体验的外观，从而为受邀合作伙伴用户提供更多上下文。

## <a name="related-articles"></a>相关文章
 在 Azure AD B2B 协作网站上浏览我们的其他文章

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [详细演练](active-directory-b2b-detailed-walkthrough.md)
* [CSV 文件格式参考](active-directory-b2b-references-csv-file-format.md)
* [外部用户令牌格式](active-directory-b2b-references-external-user-token-format.md)
* [外部用户对象属性更改](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [当前预览版限制](active-directory-b2b-current-preview-limitations.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


