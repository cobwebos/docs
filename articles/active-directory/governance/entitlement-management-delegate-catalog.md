---
title: 将访问治理委派给 Azure AD 授权管理中的目录创建者 - Azure 活动目录
description: 了解如何将访问治理从 IT 管理员委派给目录创建者和项目经理，以便他们可以自行管理访问权限。
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120194"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中将访问治理委派给目录创建者

目录是资源和访问包的容器。 需要将相关的资源和访问包分组时，可以创建目录。 默认情况下，全局管理员或用户管理员可以[创建目录](entitlement-management-catalog-create.md)，并可以添加其他用户作为目录所有者。

要委派给非管理员的用户，以便他们可以创建自己的目录，可以将这些用户添加到 Azure AD 授权管理定义的目录创建者角色。 您可以添加单个用户，也可以添加组，然后其成员就可以创建目录。  创建目录后，他们可以随后将自己拥有的资源添加到其目录中。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>作为 IT 管理员，委派给目录创建者

遵循以下步骤将用户分配到目录创建者角色。

**先决条件角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在“权利管理”部分的左侧菜单中，单击“设置”。********

1. 单击 **“编辑”**。

    ![添加目录创建者的设置](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. 在 **"委派授权管理**"部分中，单击"**添加目录创建者**"以选择要委派此授权管理角色的用户或组。

1. 单击“选择”。

1. 单击“保存”。****

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>允许委派的角色访问 Azure 门户

要允许委派的角色（如目录创建者和访问包管理器）访问 Azure 门户以管理访问包，应检查管理门户设置。

**先决条件角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，单击**Azure 活动目录**，然后单击 **"用户**"。

1. 在左侧菜单中，单击 **"用户设置**"。

1. 确保**对 Azure AD 管理门户的访问权限**设置为 **"否**"。

    ![Azure AD 用户设置 - 管理门户](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>后续步骤

- [创建和管理资源目录](entitlement-management-catalog-create.md)
- [将访问治理委派给访问包管理器](entitlement-management-delegate-managers.md)

