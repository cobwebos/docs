---
title: "管理对 Azure 堆栈 （服务管理员和租户） 中每个用户的资源的权限 |Microsoft 文档"
description: "为服务管理员或租户，了解如何管理 RBAC 权限。"
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e558f9de9bc3182bbe20ceb9d8f3f96e47fa542c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control"></a>管理基于角色的访问控制

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈中的用户可以读取器、 所有者或参与者的订阅、 资源组或服务的每个实例。 例如，用户 A 可能拥有订阅 1 读取器权限，但有虚拟机 7 所有者权限。

* 读取器： 用户可以查看所有内容，但不能进行任何更改。
* 参与者： 用户可以管理对资源的访问权限以外的所有内容。
* 所有者： 用户可以管理所有内容，包括对资源的访问。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限
1. 使用具有你想要管理的资源的所有者权限的帐户登录。
2. 在资源边栏选项卡，单击**访问**图标![](media/azure-stack-manage-permissions/image1.png)。
3. 在**用户**边栏选项卡，单击**角色**。
4. 在**角色**边栏选项卡，单击**添加**添加用户的权限。

## <a name="next-steps"></a>后续步骤
[添加 Azure Stack 租户](azure-stack-add-new-user-aad.md)

