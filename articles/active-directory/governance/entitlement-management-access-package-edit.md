---
title: 在 Azure AD 的权利管理中隐藏或删除访问包-Azure Active Directory
description: 了解如何在 Azure Active Directory 权限管理中隐藏或删除访问包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4391cd20f3919e8def28fa3c1d5007f91b2d9f0b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174747"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权限管理中隐藏或删除访问包

默认情况下，访问包是可发现的。 这意味着，如果策略允许用户请求访问包，则他们将自动看到其 "我的 Access" 门户中列出的访问包。 但是，你可以更改**隐藏**的设置，以便在用户的我的 access 门户中未列出该访问包。

本文介绍如何隐藏或删除访问包。

## <a name="change-the-hidden-setting"></a>更改隐藏设置

按照以下步骤更改访问包的**隐藏**设置。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 在 "概述" 页上，单击 "**编辑**"。

1. 设置**隐藏**设置。

    如果设置为 "**否**"，则访问包将在用户的我的访问门户中列出。

    如果设置为 **"是"** ，则访问包将不会在用户的我的访问门户中列出。 如果用户拥有访问包的 "直接**访问" 门户链接**，则用户可以查看访问包的唯一方式就是。 有关详细信息，请参阅 "[共享链接" 来请求访问包](entitlement-management-access-package-settings.md)。

## <a name="delete-an-access-package"></a>删除访问包

仅当访问包没有活动的用户分配时，才能将其删除。 请按照下列步骤操作以删除访问包。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 在左侧菜单中，单击 "**分配**" 并删除所有用户的访问权限。

1. 在左侧菜单中，单击 "**概述**"，然后单击 "**删除**"。

1. 在出现的 "删除" 消息中，单击 **"是"** 。

## <a name="next-steps"></a>后续步骤

- [查看、添加和删除访问包的分配](entitlement-management-access-package-assignments.md)
- [查看报表和日志](entitlement-management-reports.md)
