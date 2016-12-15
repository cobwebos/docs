---
title: "Azure Active Directory：创建租户支持主题 | Microsoft Docs"
description: "创建 Azure Active Directory 租户或 Azure Active Directory B2C 租户：问题和解决方法"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: msmbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c49ef7299cc0d6eec96df85675eb1f00c28c97d1


---
# <a name="creating-an-azure-active-directory-azure-ad-tenant-or-azure-ad-b2c-tenant-issues-and-resolutions"></a>创建 Azure Active Directory (Azure AD) 租户或 Azure AD B2C 租户：问题和解决方法
## <a name="creating-an-azure-ad-tenant"></a>创建 Azure AD 租户
如果第一次无法创建 Azure AD 租户，请重试。 如果问题持续出现，请联系支持部门。

## <a name="creating-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户
如果在[创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)过程中遇到问题，请尝试以下方法：

* 如果 Azure AD B2C 租户未显示在你的租户列表中，请重试。
* 如果 Azure AD B2C 租户显示在你的租户列表中，但是收到一条错误消息（“无法完成 B2C 租户‘contosob2c’的创建。 请访问此[链接](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)以获取更多指南。”），则删除刚创建的租户，然后重试。
* 请注意，当删除现有 B2C 租户并重新创建具有相同域名的 B2C 租户时，存在已知问题。 你必须创建具有不同域名的 B2C 租户。
* 如果这些解决方法不起作用，请联系支持部门。 了解[如何提出针对 Azure AD B2C 的支持请求](active-directory-b2c-support.md)的详细信息。




<!--HONumber=Nov16_HO3-->


