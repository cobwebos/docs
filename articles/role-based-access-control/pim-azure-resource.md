---
title: 使用 Azure AD Privileged Identity Management (PIM) 管理对 Azure 资源的访问权限
description: 了解如何使用 Azure Active Directory Privileged Identity Management (PIM) 和基于角色的访问控制 (RBAC) 管理对 Azure 资源的访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338194"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>使用 Azure AD Privileged Identity Management 管理对 Azure 资源的访问权限

为了保护特权帐户免受恶意网络攻击，可以使用 Azure Active Directory Privileged Identity Management (PIM) 来降低权限的暴露时间，并通过报表和警报增加对使用的可见性。 PIM 通过限制用户仅采用“实时”(JIT) 权限，或通过在缩短的持续时间内分配权限（之后权限会自动撤销）来实现这一目的。 

现在，可以将 PIM 与 Azure 基于角色的访问控制 (RBAC) 结合使用来管理、控制和监视对 Azure 资源的访问。 PIM 可以管理内置和自定义角色的成员身份，从而有助于： 

- 启用对 Azure 资源的按需“实时”访问
- 针对分配的用户和组自动使资源访问权限过期
- 将对 Azure 资源的临时访问权限分配给快速任务或正在调用的计划
- 在为新用户或组分配资源访问权限以及激活有资格的分配时获取警报

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](../active-directory/privileged-identity-management/pim-configure.md)。