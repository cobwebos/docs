---
title: 委派访问控制以访问 Azure AD 的权利管理中的包管理器-Azure Active Directory
description: 了解如何委派 IT 管理员的访问控制，以访问包管理器和项目经理，使他们能够自行管理访问权限。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174361"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>委派访问控制，以访问 Azure AD 的权利管理中的包管理器

若要委派在目录中创建和管理访问包，请将用户添加到访问包管理器角色。 访问包管理器必须熟悉用户是否需要请求对目录中资源的访问权限。 例如，如果某一目录用于某个项目，则项目主管可能是该目录的访问程序包管理器。  访问包管理器无法将资源添加到目录，但可以管理目录中的访问包和策略。  委托给访问包管理器时，该人员可以负责：

- 用户将对目录中的资源拥有哪些角色
- 需要访问权限的用户
- 谁需要批准访问请求
- 项目的最后时间

此视频概述了如何将访问管理从目录所有者委派到 access 程序包管理器。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>作为目录所有者，将委托给 access 包管理器

按照以下步骤将用户分配到访问包管理器角色：

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开要向其添加管理员的目录。

1. 在左侧菜单中，单击 "**角色和管理员**"。

    ![目录角色和管理员](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 单击 "**添加访问包管理器**" 以选择这些角色的成员。

1. 单击 "**选择**" 添加这些成员。

## <a name="remove-an-access-package-manager"></a>删除访问包管理器

请按照以下步骤从访问包管理器角色中删除用户：

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开要向其添加管理员的目录。

1. 在左侧菜单中，单击 "**角色和管理员**"。

1. 在要删除的访问包管理器旁边添加复选标记。

1. 单击“删除”。

## <a name="next-steps"></a>后续步骤

- [创建新的访问包](entitlement-management-access-package-create.md)
