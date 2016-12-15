---
title: "Azure Active Directory B2B 协作的当前预览限制 | Microsoft 文档"
description: "Azure Active Directory B2B 可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 1cc8440c-b74b-4789-9401-428105e8a00c
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 852bc6e8880525bf72e86f3a0e64e63086c0ad04


---
# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Azure AD B2B 协作预览版：当前预览版限制
* 外部用户不支持多重身份验证 (MFA)。 例如，如果 Contoso 启用了MFA，但合作伙伴组织未启用，则无法通过 B2B 协作授予合作伙伴组织用户 MFA。
* 只能通过 CSV 邀请；不支持单个邀请和 API 访问。
* 仅 Azure AD 全局管理员可以上载 .csv 文件。
* 目前不支持发送邀请到消费者电子邮件地址（例如，hotmail.com、Gmail.com 或 comcast.net）。
* 外部用户访问本地应用程序未经过测试。
* 当从目录中删除实际用户时，不会自动清除外部用户。
* 不支持发送邀请到通讯组列表。
* 最多可通过 CSV 上载 2,000 条记录。

## <a name="related-articles"></a>相关文章
在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [工作原理](active-directory-b2b-how-it-works.md)
* [详细演练](active-directory-b2b-detailed-walkthrough.md)
* [CSV 文件格式参考](active-directory-b2b-references-csv-file-format.md)
* [外部用户令牌格式](active-directory-b2b-references-external-user-token-format.md)
* [外部用户对象属性更改](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


