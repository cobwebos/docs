---
title: Microsoft 365 工作负载管理员角色内容 - Azure AD | Microsoft Docs
description: 在 Azure Active Directory 中查找针对 Microsoft 365 工作负载的管理员角色内容和 API 引用
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7460c3a7d957634d14186d313994c65d89fa4a32
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217864"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Microsoft 365 工作负载的管理员角色

在 Azure AD 中，可以使用管理角色管理 Microsoft 365 中的所有产品。 某些产品还提供特定于该产品的其他角色。 有关每个产品所支持角色的信息，请参阅下表。 有关委派问题的常规讨论，请参阅 [Azure Active Directory 中的角色委派计划](roles-concept-delegation.md)。

## <a name="where-to-find-content"></a>查找内容位置

Microsoft 365 工作负载 | 角色内容 | API 内容
---------------------- | ------------------ | -----------------
Office 365 和 Microsoft 365 业务计划中的管理员角色 | [Office 365 管理员角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | 不可用
Azure Active Directory (Azure AD) 和 Azure AD Identity Protection| [Azure AD 管理员角色](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange 基于角色的访问控制](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[提取角色分配](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD 管理员角色](directory-assign-admin-roles.md)<br>还有[关于 Office 365 中的 SharePoint 管理员角色](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype for Business | [Azure AD 管理员角色](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
安全与合规中心（Office 365 高级威胁防护、Exchange Online Protection、信息保护） | [Office 365 管理员角色](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[提取角色分配](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
安全功能分数 | [Azure AD 管理员角色](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
合规性管理器 | [合规性管理器角色](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 不可用
Azure 信息保护 | [Azure AD 管理员角色](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [基于角色的访问控制](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API 参考](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure 高级威胁防护 | [Azure ATP 角色组](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | 不可用
Windows Defender 高级威胁防护 | [Windows Defender ATP 基于角色的访问控制](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 不可用
Privileged Identity Management | [Azure AD 管理员角色](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune 基于角色的访问控制](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[提取角色分配](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
托管桌面 | [Azure AD 管理员角色](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>后续步骤

* [如何分配或删除 Azure AD 管理员角色](directory-manage-roles-portal.md)
* [Azure AD 管理员角色参考](directory-assign-admin-roles.md)
