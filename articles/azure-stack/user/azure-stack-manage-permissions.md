---
title: 管理 Azure Stack 中每个用户对资源的权限 | Microsoft Docs
description: 作为服务管理员或租户，了解如何管理 RBAC 权限。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4f9354426ba584b26213f8a104c14122a831a453
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>管理使用 Azure Stack Role-Based 访问控制对资源的访问权限

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈支持基于角色的访问控制 (RBAC)，相同[访问管理的安全模型](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview)Microsoft Azure 使用。 可以使用 RBAC 来管理用户、 组或订阅、 资源和服务应用程序访问。

## <a name="basics-of-access-management"></a>访问管理的基础知识

基于角色的访问控制，您可以使用来保护你的环境的精细的访问控制。 通过分配在某些范围内的 RBAC 角色所需的确切权限授予用户。 角色分配的作用域可以是订阅、 资源组或单个资源。 读取[在 Azure 门户中基于角色的访问控制](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview)文，以获取有关访问管理的更多详细的信息。

### <a name="built-in-roles"></a>内置角色

Azure 堆栈具有三个基本角色可应用于所有资源类型：

* **所有者**可以管理所有内容，包括对资源的访问。
* **参与者**可以管理对资源的访问权限以外的所有内容。
* **读取器**可以查看所有内容，但不能进行任何更改。

### <a name="resource-hierarchy-and-inheritance"></a>资源层次结构和继承

Azure 堆栈具有以下资源层次结构：

* 每个订阅都属于一个目录。
* 每个资源组属于一个订阅。
* 每个资源属于一个资源组。

在子范围继承在父作用域授予的访问。 例如：

* 将读取器角色分配给订阅范围内的 Azure AD 组。 该组成员可以查看订阅中的每个资源组和资源。
* 将参与者角色分配给资源组范围内的应用程序。 应用程序可以管理该资源组，但不是在订阅中的其他资源组中的所有类型的资源。

### <a name="assigning-roles"></a>分配角色

你可以将多个角色分配给用户，并且每个角色可以与不同的作用域关联。 例如：

* 你将 TestUser-A 读取者角色分配给订阅-1。
* 将 TestUser-A 所有者角色分配到 TestVM-1。

Azure[角色分配](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal)文章提供了有关查看、 分配和删除角色的详细的信息。

### <a name="resource-hierarchy-and-inheritance"></a>资源层次结构和继承

Azure 堆栈具有以下资源层次结构：

* 每个订阅都属于一个目录。
* 每个资源组属于一个订阅。
* 每个资源属于一个资源组。

在子范围继承在父作用域授予的访问。 例如：

* 你分配**读取器**到 Azure AD 组在订阅范围内的角色。 该组成员可以查看订阅中的每个资源组和资源。
* 你分配**参与者**到应用程序在资源组作用域的角色。 应用程序可以管理该资源组，但不是在订阅中的其他资源组中的所有类型的资源。

### <a name="assigning-roles"></a>分配角色

你可以将多个角色分配给用户，并且每个角色可以与不同的作用域关联。 例如：

* 你将 TestUser-A 读取者角色分配给订阅-1。
* 将 TestUser-A 所有者角色分配到 TestVM-1。

Azure[角色分配](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal)文章提供了有关查看、 分配和删除角色的详细的信息。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

以下步骤介绍如何配置用户权限。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在左侧导航窗格中，选择“资源组”。
3. 选择你想要设置权限的资源组的名称。
4. 在资源组的导航窗格中，选择**访问控制 (IAM)**。 **访问控制**视图列出有权访问的资源组的项目。 你可以筛选这些结果，并使用菜单栏添加或删除权限。
5. 上**访问控制**菜单栏上，选择 **+ 添加**。
6. 上**添加权限**:

   * 选择你想要从分配的角色**角色**下拉列表。
   * 选择你想要从分配的资源**分配其访问权限**下拉列表。
   * 在你想要授予访问权限的目录中选择用户、组或应用程序。 可以通过显示名称、电子邮件地址和对象标识符搜索该目录。

7. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[创建服务主体](azure-stack-create-service-principals.md)