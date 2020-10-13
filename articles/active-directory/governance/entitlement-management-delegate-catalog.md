---
title: 在 Azure AD 权利管理中将访问权限管理委派给的目录创建者 - Azure Active Directory
description: 了解如何将访问权限管理从 IT 管理员委托给目录创建者和项目经理，使他们能够自行管理访问权限。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798420"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中将访问权限管理委派给的目录创建者

目录是资源和访问包的容器。 需要将相关的资源和访问包分组时，可以创建目录。 默认情况下，全局管理员或用户管理员可以[创建目录](entitlement-management-catalog-create.md)，并且可以将其他用户添加为目录所有者。

若要委派给不是管理员的用户，以便他们可以创建自己的目录，可以将这些用户添加到 Azure AD 权利管理定义的目录创建者角色。 可以添加单个用户，也可以添加一个组，使其成员可以创建目录。  创建目录后，他们就可以将自己拥有的资源添加到其目录。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>以 IT 管理员身份委派给目录创建者

遵循以下步骤将用户分配到目录创建者角色。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在“权利管理”部分的左侧菜单中，单击“设置”。********

1. 单击 **“编辑”** 。

    ![用于添加目录创建者的设置](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. 在“委托权利管理”部分，单击“添加目录创建者”，以选择要向其委托此权利管理角色的用户或组 。

1. 单击“选择”。

1. 单击“保存”。

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>允许受委托的角色访问 Azure 门户

若要允许受委托的角色（例如目录创建者和访问包管理者）访问 Azure 门户以管理访问包，应检查管理门户设置。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，单击“Azure Active Directory”，然后单击“用户” 。

1. 在左侧的菜单中，单击“用户设置”。

1. 确保将“限制访问 Azure AD 管理门户”设置为“否” 。

    ![Azure AD 用户设置 - 管理门户](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>后续步骤

- [创建和管理资源的目录](entitlement-management-catalog-create.md)
- [将访问权限管理委托给访问包管理者](entitlement-management-delegate-managers.md)

