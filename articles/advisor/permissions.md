---
title: Azure 顾问中的权限
description: 顾问权限和如何，它们可能会阻止您配置订阅或推迟或消除建议的能力。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467602"
---
# <a name="permissions-in-azure-advisor"></a>Azure 顾问中的权限

Azure 顾问提供了基于使用情况和配置 Azure 资源和订阅的建议。 顾问将使用[内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)提供[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)(RBAC) 来管理你对建议和顾问的功能。 

## <a name="roles-and-their-access"></a>角色和他们的访问权限

下表定义的角色和顾问中的访问：

| **角色** | **查看建议** | **编辑规则** | **编辑订阅配置** | **编辑资源组配置**| **关闭并推迟建议**|
|---|:---:|:---:|:---:|:---:|:---:|
|订阅所有者|**X**|**X**|**X**|**X**|**X**|
|订阅参与者|**X**|**X**|**X**|**X**|**X**|
|订阅读者|**X**|--|--|--|--|
|资源组所有者|**X**|--|--|**X**|**X**|
|资源组参与者|**X**|--|--|**X**|**X**|
|读取器的资源组|**X**|--|--|--|--|
|资源所有者|**X**|--|--|--|**X**|
|资源参与者|**X**|--|--|--|**X**|
|资源读取器|**X**|--|--|--|--|

> [!NOTE]
> 查看建议的访问权限是依赖于您对建议的受影响资源访问权限。

## <a name="permissions-and-unavailable-actions"></a>权限和不可用的操作

缺乏适当的权限可能会阻止您在顾问中执行操作的能力。 以下是一些常见的问题。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>无法将订阅或资源组

当你尝试在顾问中配置订阅或资源组时，可能会看到包含或排除选项会被禁用。 此状态指示没有足够的权限为该资源组或订阅级别。 若要解决此问题，了解如何[授予用户访问权限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>无法推迟或消除建议

如果尝试将推迟或消除建议时收到错误，您可能没有足够的权限。 请确保至少具有对受影响资源的建议要推迟或消除的参与者访问权限。 若要解决此问题，了解如何[授予用户访问权限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

## <a name="next-steps"></a>后续步骤

本文概要介绍顾问如何使用 RBAC 控制用户权限，以及如何解决常见的问题了。 若要详细了解顾问，请参阅以下资源：

- [什么是 Azure 顾问？](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Azure 顾问入门](https://docs.microsoft.com/azure/advisor/advisor-get-started)
