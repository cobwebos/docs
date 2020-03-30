---
title: 将访问治理委派到访问 Azure AD 授权管理中的包管理器 - Azure 活动目录
description: 了解如何将访问治理委派给 IT 管理员以访问包经理和项目经理，以便他们能够自行管理访问。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174361"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>将访问治理委派给 Azure AD 授权管理中的包管理器

要委托目录中访问包的创建和管理，可以将用户添加到访问包管理器角色。 访问包管理员必须熟悉用户请求访问目录中资源的需求。 例如，如果目录用于项目，则项目潜在顾客可能是该目录的访问包管理器。  访问包管理器无法将资源添加到目录中，但他们可以管理目录中的访问包和策略。  然后，当委派给访问包管理器时，此人可以负责：

- 用户对目录中的资源将具有什么角色
- 谁需要访问
- 谁需要批准访问请求
- 项目将持续多长时间

本视频概述了如何将访问治理从目录所有者委派为访问包管理器。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>作为目录所有者，委托给访问包管理器

按照以下步骤将用户分配给访问包管理器角色：

**先决条件角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“目录”，然后打开要将管理员添加到的目录。****

1. 在左侧菜单中，单击“角色和管理员”。****

    ![编目角色和管理员](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 单击"**添加访问包管理器**"以选择这些角色的成员。

1. 单击“选择”以添加这些成员。****

## <a name="remove-an-access-package-manager"></a>删除访问包管理器

按照以下步骤从访问包管理器角色中删除用户：

**先决条件角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“目录”，然后打开要将管理员添加到的目录。****

1. 在左侧菜单中，单击“角色和管理员”。****

1. 在要删除的访问包管理器旁边添加复选标记。

1. 单击 **“删除”**。

## <a name="next-steps"></a>后续步骤

- [创建新的访问包](entitlement-management-access-package-create.md)
