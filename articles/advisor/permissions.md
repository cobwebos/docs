---
title: Azure 顾问中的权限
description: 顾问权限及其如何阻止您配置订阅或推迟或拒绝建议的能力。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422316"
---
# <a name="permissions-in-azure-advisor"></a>Azure 顾问中的权限

Azure 顾问根据 Azure 资源和订阅的使用和配置提供建议。 顾问使用[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)（RBAC） 提供的[内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)来管理您对建议和顾问功能的访问。 

## <a name="roles-and-their-access"></a>角色及其访问权限

下表定义了他们在 Advisor 中的角色和访问权限：

| **作用** | **查看建议** | **编辑规则** | **编辑订阅配置** | **编辑资源组配置**| **取消和推迟建议**|
|---|:---:|:---:|:---:|:---:|:---:|
|订阅所有者|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|
|订阅参与者|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|
|订阅读取器|**Ⅹ**|--|--|--|--|
|资源组所有者|**Ⅹ**|--|--|**Ⅹ**|**Ⅹ**|
|资源组参与者|**Ⅹ**|--|--|**Ⅹ**|**Ⅹ**|
|资源组读取器|**Ⅹ**|--|--|--|--|
|资源所有者|**Ⅹ**|--|--|--|**Ⅹ**|
|资源贡献者|**Ⅹ**|--|--|--|**Ⅹ**|
|资源读取器|**Ⅹ**|--|--|--|--|

> [!NOTE]
> 查看建议的访问取决于您对建议受影响资源的访问。

## <a name="permissions-and-unavailable-actions"></a>权限和不可用操作

缺少适当的权限可能会阻止您在 Advisor 中执行操作的能力。 以下是一些常见问题。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>无法配置订阅或资源组

当您尝试在 Advisor 中配置订阅或资源组时，您可能会看到已禁用包含或排除的选项。 此状态表示您没有足够的权限级别用于该资源组或订阅。 要解决此问题，了解如何[授予用户访问权限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>无法推迟或拒绝建议

如果在尝试推迟或关闭建议时收到错误，则可能没有足够的权限。 请确保您至少具有对要推迟或关闭的建议的影响资源的贡献者访问权限。 要解决此问题，了解如何[授予用户访问权限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

## <a name="next-steps"></a>后续步骤

本文概述了 Advisor 如何使用 RBAC 来控制用户权限以及如何解决常见问题。 若要详细了解顾问，请参阅以下资源：

- [什么是 Azure 顾问？](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [开始使用 Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-get-started)
