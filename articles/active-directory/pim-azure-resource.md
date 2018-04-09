---
title: 使用 Privileged Identity Management (PIM) 管理对 Azure 资源的访问
description: 了解如何使用 PIM 中基于角色的访问管理访问 Azure 资源。
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 1e74579ef2f0e18f23a40dfc573177938b9b726f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>使用 Privileged Identity Management 管理对 Azure 资源的访问

为了保护特权帐户免受恶意网络攻击，可以使用 Azure Active Directory Privileged Identity Management (PIM) 来降低权限的暴露时间，并通过报表和警报增加对使用的可见性。 PIM 通过限制用户仅采用“实时”(JIT) 权限，或通过在缩短的持续时间内分配权限（之后权限会自动撤销）来实现这一目的。 

现在，可以将 PIM 与 Azure 基于角色的访问控制 (RBAC) 结合使用来管理、控制和监控对 Azure 资源的访问。 PIM 可以管理内置和自定义角色的成员身份，从而有助于： 

- 启用对 Azure 资源的按需“实时”访问
- 针对分配的用户和组自动使资源访问权限过期
- 将对 Azure 资源的临时访问权限分配给快速任务或正在调用的计划
- 在为新用户或组分配资源访问权限以及激活符合条件的分配时，获取警报

有关详细信息，请参阅 [Azure PIM 中基于角色的访问控制概述](privileged-identity-management/azure-pim-resource-rbac.md)。