---
title: "Azure Active Directory B2B 协作 | Microsoft 文档"
description: "Azure Active Directory B2B 协作可让业务合作伙伴访问你的企业应用程序，合作伙伴的每个用户将由单个 Azure AD 帐户表示"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f6a044c-0b36-4a1d-8080-33e28696c42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: ba3690084439aac83c91a1b4cfb7171b74c814f8
ms.openlocfilehash: 8207aa4a028b47335cb249805f7bdb362bef14a9


---
# <a name="azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 协作
Azure Active Directory (Azure AD) B2B 协作可让你通过合作伙伴管理的标识来访问企业应用程序。 你可以通过邀请并授权合作伙伴公司的用户访问你的资源，来创建跨公司关系。 由于每家公司只与 Azure Active Directory 联合一次，并且每位用户都由单个 Azure AD 帐户来表示，因此降低了复杂性。 如果业务合作伙伴在 Azure AD 中管理其帐户，则会提高安全性，因为当合作伙伴用户从其组织离职时，其访问权限即会吊销，而无法通过内部目录中的成员身份进行意外的访问。 对于尚未部署 Azure AD 的业务合作伙伴，B2B 协作简化了注册体验，可以更方便地向业务合作伙伴提供 Azure AD 帐户。

* 业务合作伙伴可以使用他们自己的登录凭据，因此你无需管理外部合作伙伴目录，也无需在用户离开合作伙伴组织时删除访问权限。
* 你可以独立于业务合作伙伴的帐户生命周期管理对应用的访问。 例如，这意味着你可以自行吊销访问权限，而不必请求业务合作伙伴的 IT 部门执行任何操作。

## <a name="capabilities"></a>功能
B2B 协作简化了管理并改进了合作伙伴访问企业资源（包括 Office 365、Salesforce、Azure 服务等 SaaS 应用，以及每个移动、云和本地声明感知应用程序）时的安全性。 B2B 协作可让合作伙伴管理自己的帐户，企业可以将安全策略应用于合作伙伴访问权限。

Azure Active Directory B2B 协作易于配置，各种规模的合作伙伴都能通过电子邮件验证过程轻松完成注册，即使他们没有自己的 Azure Active Directory。 维护也很容易，无需外部目录或基于合作伙伴的联合配置。

## <a name="b2b-collaboration-process"></a>B2B 协作流程
1. Azure AD B2B 协作允许公司管理员将一个不超过 2000 行的逗号分隔值 (CSV) 文件上载到 B2B 协作门户，以便邀请和授权一组外部用户。

   ![CSV 文件上载对话框](./media/active-directory-b2b-collaboration-overview/upload-csv.png)
2. 门户发送电子邮件邀请给这些外部用户。
3. 受邀的用户通过 Microsoft 登录现有工作帐户（在 Azure AD 中管理），或者在 Azure AD 中获取新的工作帐户。
4. 登录之后，用户将重定向到与他们共享的应用。

目前不支持邀请消费者电子邮件地址（例如，Gmail 或 [*comcast.net*](http://comcast.net/)）。

有关 B2B 协作工作原理的详细信息，请观看[此视频](http://aka.ms/aadshowb2b)。

## <a name="next-steps"></a>后续步骤
在 Azure AD B2B 协作网站上浏览我们的其他文章。

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [工作原理](active-directory-b2b-how-it-works.md)
* [详细演练](active-directory-b2b-detailed-walkthrough.md)
* [CSV 文件格式参考](active-directory-b2b-references-csv-file-format.md)
* [外部用户令牌格式](active-directory-b2b-references-external-user-token-format.md)
* [外部用户对象属性更改](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [当前预览版限制](active-directory-b2b-current-preview-limitations.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Nov16_HO3-->


