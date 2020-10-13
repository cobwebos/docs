---
title: Azure 顾问中的权限
description: 介绍顾问权限以及如何通过这些权限来阻止用户配置订阅或者推迟或取消建议。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712930"
---
# <a name="permissions-in-azure-advisor"></a>Azure 顾问中的权限

Azure 顾问根据 Azure 资源和订阅的使用情况和配置来提供建议。 顾问使用[基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 提供的[内置角色](../role-based-access-control/built-in-roles.md)来管理你对建议和顾问功能的访问。 

## <a name="roles-and-their-access"></a>角色及其访问权限

下表定义了角色及其在顾问中的访问权限：

| **角色** | **查看建议** | **编辑规则** | **编辑订阅配置** | **编辑资源组配置**| **取消和推迟建议**|
|---|:---:|:---:|:---:|:---:|:---:|
|订阅所有者|**X**|**X**|**X**|**X**|**X**|
|订阅参与者|**X**|**X**|**X**|**X**|**X**|
|订阅读者|**X**|--|--|--|--|
|资源组所有者|**X**|--|--|**X**|**X**|
|资源组参与者|**X**|--|--|**X**|**X**|
|资源组读取者|**X**|--|--|--|--|
|资源所有者|**X**|--|--|--|**X**|
|资源参与者|**X**|--|--|--|**X**|
|资源读取者|**X**|--|--|--|--|

> [!NOTE]
> 是否能够通过访问来查看建议取决于是否能够访问建议所影响的资源。

## <a name="permissions-and-unavailable-actions"></a>权限和不可用操作

缺少适当的权限会使你无法在顾问中执行操作。 下面是一些常见问题。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>无法配置订阅或资源组

尝试在顾问中配置订阅或资源组时，可能会看到“包括”或“排除”选项已禁用。 此状态表明你对该资源组或订阅没有足够级别的权限。 若要解决此问题，请了解如何[授予用户访问权限](../role-based-access-control/quickstart-assign-role-user-portal.md)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>无法推迟或取消建议

如果在尝试推迟或取消建议时收到错误，可能是因为你没有足够的权限。 请确保你至少对要推迟或取消的建议所影响的资源具有参与者访问权限。 若要解决此问题，请了解如何[授予用户访问权限](../role-based-access-control/quickstart-assign-role-user-portal.md)。

## <a name="next-steps"></a>后续步骤

本文概述了顾问如何使用 Azure RBAC 来控制用户权限以及如何解决常见问题。 若要详细了解顾问，请参阅以下资源：

- [什么是 Azure 顾问？](./advisor-overview.md)
- [Azure 顾问入门](./advisor-get-started.md)
