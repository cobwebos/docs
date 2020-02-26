---
title: Azure 安全中心常见问题解答-有关权限的问题
description: 此常见问题回答有关 Azure 安全中心中的权限的问题，这是一个可帮助你预防、检测和响应威胁的产品。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599387"
---
# <a name="permissions"></a>权限

## <a name="how-do-permissions-work-in-azure-security-center"></a>如何在 Azure 安全中心使用权限？

Azure 安全中心使用[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../role-based-access-control/built-in-roles.md)。

安全中心会评估资源的配置以识别安全问题和漏洞。 如果分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，则仅可在安全中心看到与资源相关的信息。

若要深入了解安全中心中的角色和允许的操作，请参阅 [Azure 安全中心中的权限](security-center-permissions.md)。



## <a name="who-can-modify-a-security-policy"></a>哪些用户可以修改安全策略？

若要修改安全策略，用户必须是安全管理员或是该订阅的所有者或参与者。

若要了解如何配置安全策略，请参阅[在 Azure 安全中心设置安全策略](tutorial-security-policy.md)。