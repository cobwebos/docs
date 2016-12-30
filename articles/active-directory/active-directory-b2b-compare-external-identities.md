---
title: "比较使用 Azure Active Directory 管理外部标识的功能 | Microsoft 文档"
description: "比较支持外部标识的身份验证和授权的 Azure Active Directory B2B 协作、B2C 和多租户应用"
services: active-directory
documentationcenter: 
author: arvindsuthar
manager: cliffdi
editor: 
tags: 
ms.assetid: 749b2e3a-2b8e-45ba-9f2a-6ca56eb1053b
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/24/2016
ms.author: asuthar
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a9a7c0a9adea7fe4fccaa4ec9df0b077cd1e7382


---
# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>比较使用 Azure Active Directory 管理外部标识的功能
除了管理移动办公人员对 SaaS 应用的访问外，Azure Active Directory (Azure AD) 还可以帮助组织与业务合作伙伴共享资源，并可以将应用程序交付给企业和使用者。

## <a name="developing-applications-for-businesses"></a>为企业开发应用程序
是否向企业提供服务或应用程序（如工资单服务）？ Azure AD 提供的标识平台可用于构建与数百万个组织无缝集成的应用程序，这些组织在部署 Office 365 或其他企业服务的过程中已配置 Azure AD。

**示例：**医药经销商向医疗行业提供医疗用品和信息系统。 他们需要将分析应用程序安排到医疗实践中，并想要客户管理其自己的标识。 此公司选择了 Azure AD 作为其实践管理应用程序的标识平台，并在其客户注册时向其提供 Azure AD 标识（如果必要）。 有关详细信息，请参阅 [Azure Active Directory 开发人员指南](active-directory-developers-guide.md)。

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>允许业务合作伙伴访问公司资源
你是否有业务合作伙伴或公司外面的其他人需要访问你的企业资源，如 SharePoint 站点或 ERP 系统？ Azure AD 允许管理员授予外部用户（不一定存在于 Azure AD 中）单一登录企业应用程序的访问权限。 这可以提高安全性，因为当用户离开合作伙伴组织时，他们将失去访问权，而你可以控制组织中的访问策略。 这还简化了管理，因为无需管理外部合作伙伴目录或每个合作伙伴联合身份验证关系。

**示例：**影像公司向零售商提供照片影像服务，并运营最大的印刷亭零售网络。 他们选择 Azure AD，以允许其零售业务合作伙伴的数千名用户使用其自己的凭据下载最新的合作伙伴营销材料并从公司的供应商外部网再订购照片处理物料。 有关详细信息，请参阅 [Azure AD B2B 协作](active-directory-b2b-what-is-azure-ad-b2b.md)。

## <a name="developing-applications-for-consumers"></a>为使用者开发应用程序
是否需要向数百万使用者安全地、经济高效地发布联机应用程序，如零售店前端？ Azure AD 提供了一个平台，为应用程序使用者启用社交以及用户名/密码登录、经过品牌打造的自助服务注册和自助服务密码重置。 这可增加使用者的便利性，同时可降低开发人员的负担。

**示例：** \#1 世界职业运动联盟需要直接与其 4.5 亿粉丝接洽。 为此，他们使用 Azure AD 构建了一个移动应用，供用户进行身份验证和配置文件存储。 粉丝通过使用 Facebook 等社交帐户可以简化注册和登录，也以可以使用传统的用户名/密码实现跨 iOS、Android 和 Windows 手机的无缝体验。 在已建立的 Azure AD 平台上构建显著减少了自定义代码，同时提供特许经营自定义品牌，并减轻有关安全性、 数据外泄和可伸缩性的顾虑。 有关详细信息，请参阅 [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)。

## <a name="comparison-of-azure-ad-capabilities"></a>Azure AD 功能的比较
本文中已讨论的每个方案均借助 Azure AD 中的功能实现。 下表可以帮助弄清哪些功能与你最相关：

| **请考虑此产品...** | [Azure AD 多租户 SaaS 应用](active-directory-developers-guide.md) | [Azure AD B2B 协作](active-directory-b2b-what-is-azure-ad-b2b.md) | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) |
| --- | --- | --- | --- |
| **如果我需要提供...** |为企业提供服务 |为提供合作伙伴对我的应用的访问权限 |为使用者提供服务 |
| **并且我类似于...** |医药经销商 |影像公司 |职业运动联盟 |
| **部署应用用于...** |实践管理 |供应商外部网 |足球迷 |
| **面向...** |医生办公室 |已批准的业务合作伙伴 |具有电子邮件的任何人 |
| **在以下情况下可访问...** |客户管理员同意 |我的管理员邀请 |使用者注册 |




<!--HONumber=Dec16_HO5-->


