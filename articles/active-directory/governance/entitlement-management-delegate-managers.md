---
title: 将访问治理委托给 Azure AD 授权管理中的访问包管理者 - Azure Active Directory
description: 了解如何将 IT 管理员的访问治理委托给访问包管理者和项目经理，使他们能够自行管理访问权限。
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
ms.openlocfilehash: e81389ec953829115062a31a019caf040850cbea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798625"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>将访问治理委托给 Azure AD 权利管理中的访问包管理者

若要委托目录中访问包的创建和管理，请将用户添加到访问包管理者角色。 访问包管理者必须熟悉用户请求访问目录中资源的需求。 例如，如果目录用于项目，则项目主管可能是该目录的访问包管理者。  访问包管理者无法向目录添加资源，但他们可以管理目录中的访问包和策略。  委托给访问包管理者后，这个人将负责管理：

- 用户对目录中资源需要什么角色
- 谁需要访问权限
- 谁需要批准访问请求
- 项目将持续多久

本视频概述了如何将目录所有者的访问治理委托给访问包管理者。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>以目录所有者的身份，委托给访问包管理者

遵循以下步骤将用户分配给访问包管理者角色：

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“目录”，然后打开要将管理员添加到的目录。****

1. 在左侧菜单中，单击“角色和管理员”。****

    ![目录角色和管理员](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 单击“添加访问包管理者”，以选择这些角色的成员。

1. 单击“选择”以添加这些成员。****

## <a name="remove-an-access-package-manager"></a>删除访问包管理者

按照以下步骤将用户从访问包管理者角色中删除：

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“目录”，然后打开要将管理员添加到的目录。****

1. 在左侧菜单中，单击“角色和管理员”。****

1. 在要删除的访问包管理者旁边添加一个复选标记。

1. 单击 **“删除”** 。

## <a name="next-steps"></a>后续步骤

- [创建新的访问包](entitlement-management-access-package-create.md)
