---
title: "适用于 Azure Active Directory B2B 协作预览版的外部用户令牌格式 | Microsoft 文档"
description: "Azure Active Directory B2B 可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 66df5c92-1603-4d26-966b-c06cbfb7931a
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7edfbd9884d337e80a449f51af6abd962d138ddb


---
# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Azure AD B2B 协作预览版：外部用户令牌格式
azure.microsoft.com 上的[支持的令牌和声明类型](active-directory-token-and-claims.md)一文中介绍了标准 Azure AD 令牌的声明。

对于经过身份验证的 B2B 协作外部用户来说是不同的声明如下所示：<br/>

* **OID**：资源租户的对象 ID<br/>
* **TID**：资源租户的租户 ID<br/>
* **Issuer**：这是资源租户<br/>
* **IDP**：这是用户的主租户<br/>
* **AltSecId**：这是难理解的备用安全 ID<br/>

## <a name="related-articles"></a>相关文章
在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [工作原理](active-directory-b2b-how-it-works.md)
* [详细演练](active-directory-b2b-detailed-walkthrough.md)
* [CSV 文件格式参考](active-directory-b2b-references-csv-file-format.md)
* [外部用户对象属性更改](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [当前预览版限制](active-directory-b2b-current-preview-limitations.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


