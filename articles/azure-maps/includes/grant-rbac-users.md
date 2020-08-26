---
title: 为用户授予基于角色的访问权限
titleSuffix: Azure Maps
description: 使用基于角色的访问控制向用户授予授权 Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 816e4e439bb98ced8bbc5b5d8a7d38ef65aee33f
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011028"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>向用户授予 Azure Maps 的基于角色的访问权限

可以通过将 Azure AD 组或安全主体分配给一个或多个 Azure Maps 角色定义，向 azure *RBAC) 授予 azure 基于角色的访问控制 (* 。 若要查看可用于 Azure Maps 的 Azure 角色定义，请**访问 (IAM) 的 "访问控制**"。 选择 "**角色**"，然后搜索以*Azure Maps*开头的角色。

* 若要有效地管理大量用户对 Azure Maps 的访问权限，请参阅[Azure AD 组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)。
* 对于允许用户对应用程序进行身份验证的用户，必须在 Azure AD 中创建用户。 请参阅[添加或删除使用 Azure AD 的用户](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)。

阅读有关[Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/)的详细信息，以便有效地管理用户的目录。

1. 中转到你的**Azure Maps 帐户**。 ** (IAM) **  >  **角色分配**选择 "访问控制"。

    ![使用 Azure RBAC 授予访问权限](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 "**角色分配**" 选项卡上的 "**角色**" 下，选择内置 Azure Maps 角色定义，如**Azure Maps 数据读取器**或**Azure Maps 数据参与者**。 在“分配访问权限至”下，选择“Azure AD 用户、组或服务主体”********。 按名称选择主体。 再选择“保存”。

   * 有关[添加或删除角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)的详细信息，请参阅。

> [!WARNING]
> Azure Maps 内置角色定义提供对许多 Azure Maps REST Api 的一种非常大的授权访问权限。 若要限制用户至少使用 Api，请参阅[创建自定义角色定义和将用户分配](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)到自定义角色定义。 这样，用户就可以拥有应用程序所需的最小特权。