---
title: 在 Azure Active Directory B2C 中创建租户故障排除 | Microsoft Docs
description: 创建 Azure Active Directory 租户或 Azure Active Directory B2C 租户时出现的问题和解决方法。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 90d9d2fb80dfbd094754850b7d1270a5fafcdd96
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712498"
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

