---
title: "Azure Active Directory B2C：创建租户故障排除 | Microsoft Docs"
description: "创建 Azure Active Directory 租户或 Azure Active Directory B2C 租户时出现的问题和解决方法。"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 81af4536fc223319369aff262d42149cfbf1a1e9
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>创建 Azure Active Directory 租户或 Azure Active Directory B2C 租户故障排除 

## <a name="create-an-azure-ad-tenant"></a>创建 Azure AD 租户
如果第一次尝试时无法创建 Azure Active Directory (Azure AD) 租户，请重试。 如果问题持续出现，请联系 Azure 支持部门。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户
如果在[创建 Azure Active Directory B2C (Azure AD B2C) 租户](active-directory-b2c-get-started.md)时遇到问题，请尝试以下选项：

* 如果 Azure AD B2C 租户未在租户列表中显示，请重新尝试创建租户。
* 如果 Azure AD B2C 租户未在租户列表中显示并看到以下错误消息，请删除租户，然后重新创建：

    “Could not complete the creation of the B2C tenant 'contosob2c'. Please visit this [link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) for more guidance.”（无法创建 B2C 租户“contosob2c”。请访问此链接获取更多指导。）
* 删除现有 Azure AD B2C 租户并使用相同域名重新创建时，存在已知问题。 创建新的 Azure AD B2C 租户时，必须使用不同的域名。
* 如果这些解决方法不起作用，请联系 Azure 支持部门。 有关详细信息，请参阅 [Azure AD B2C 的文件支持请求](active-directory-b2c-support.md)。


