---
title: Azure Advisor 中的权限
description: Advisor 权限以及这些权限如何阻止你配置订阅、推迟或取消建议。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422316"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor 中的权限

Azure 顾问根据 Azure 资源和订阅的使用情况和配置来提供建议。 顾问使用[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)（RBAC）提供的[内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)来管理对建议和顾问功能的访问。 

## <a name="roles-and-their-access"></a>角色及其访问

下表定义角色以及它们在 Advisor 中的访问权限：

| **角色** | **查看建议** | **编辑规则** | **编辑订阅配置** | **编辑资源组配置**| **消除并推迟建议**|
|---|:---:|:---:|:---:|:---:|:---:|
|订阅所有者|**X**|**X**|**X**|**X**|**X**|
|订阅参与者|**X**|**X**|**X**|**X**|**X**|
|订阅读取器|**X**|--|--|--|--|
|资源组所有者|**X**|--|--|**X**|**X**|
|资源组参与者|**X**|--|--|**X**|**X**|
|资源组读取器|**X**|--|--|--|--|
|资源所有者|**X**|--|--|--|**X**|
|资源参与者|**X**|--|--|--|**X**|
|资源读取器|**X**|--|--|--|--|

> [!NOTE]
> 查看建议的访问权限取决于对建议的受影响资源的访问权限。

## <a name="permissions-and-unavailable-actions"></a>权限和不可用操作

缺少适当的权限会阻止你在 Advisor 中执行操作。 下面是一些常见问题。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>无法配置订阅或资源组

尝试在 Advisor 中配置订阅或资源组时，可能会看到 "包括" 或 "排除" 选项已禁用。 此状态表明你没有足够的权限来执行该资源组或订阅。 若要解决此问题，请了解如何[授予用户访问权限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>无法推迟或消除建议

如果尝试推迟或消除建议时收到错误，可能是因为没有足够的权限。 请确保至少具有参与者访问要推迟或消除的建议的受影响资源的访问权限。 若要解决此问题，请了解如何[授予用户访问权限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

## <a name="next-steps"></a>后续步骤

本文概述了顾问如何使用 RBAC 来控制用户权限以及如何解决常见问题。 若要详细了解顾问，请参阅以下资源：

- [什么是 Azure 顾问？](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Azure Advisor 入门](https://docs.microsoft.com/azure/advisor/advisor-get-started)
