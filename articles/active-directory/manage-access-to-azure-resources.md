---
title: 使用 Azure Active Directory 管理对 Azure 资源的访问权限
description: 了解如何使用 Azure Active Directory 的各项功能管理 Azure 资源访问权限。
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: eee4353c183aeec19f72f8e1dec6c20b6c5bb226
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2018
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>使用 Azure Active Directory 管理对 Azure 资源的访问权限

对于任何使用云的组织而言，云资源的标识和权限管理都是一项重要功能。 Azure Active Directory (Azure AD) 是 Microsoft Azure 的标识和访问系统。  

在浏览 Azure AD 的支持功能区域之前，请查看以下视频：“使用 SSO、基于角色的访问控制和条件锁定对 Azure 云的访问”。 在其中，你将学习：

- 使用本地 Active Directory 配置 Azure 门户单一登录的最佳做法。
- 使用 Azure RBAC 对订阅中的资源进行精细的访问控制。
- 使用 Azure AD 条件访问强制执行强身份验证规则。
- 托管服务标识的概念，其中 Azure 资源可以自动向 Azure 服务进行身份验证，并且开发人员无需处理 API 密钥或机密。

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>功能区域
Azure AD 就管理 Azure 资源访问权限提供以下功能：

|||
|---|---|
| [Azure AD 租户和订阅的关系](active-directory-understanding-resource-access.md) | 了解 Azure AD 何以是 Azure 订阅用户和组的受信任源。 |
| [基于角色的访问控制 (RBAC)](role-based-access-control-what-is.md) | 通过分配给用户、组或服务主体的角色，提供精确的访问管理。 |
| [Privileged Identity Management 与 RBAC](pim-azure-resource.md) | 通过实时分配特权角色，控制使用高级特权的访问。 |
| [用于 Azure 管理的条件访问](conditional-access-azure-management.md) | 设置条件访问策略，以允许或阻止对 Azure 管理终结点的访问。 |
| [托管的服务标识 (MSI)](msi-overview.md) | 向虚拟机等 Azure 资源提供各自的标识，以免在代码中添加凭据。 |

 