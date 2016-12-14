---
title: "开发测试实验室概念 | Microsoft Docs"
description: "了解开发测试实验室的基本概念及其如何轻松地创建、管理和监视 Azure 虚拟机"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e1b087b9e952d9045e8580d4074e7655d165a08


---
# <a name="devtest-labs-concepts"></a>开发测试实验室概念
> [!NOTE]
> 本系列共有 3 部分，本文是第 3 部分：
> 
> 1. [什么是开发测试实验室？](devtest-lab-overview.md)
> 2. [为何选择开发测试实验室？](devtest-lab-why.md)
> 3. **[开发测试实验室概念](devtest-lab-concepts.md)**
> 
> 

## <a name="overview"></a>概述
下表包含关键开发测试实验室概念和定义：

## <a name="artifacts"></a>项目
项目用于在预配 VM 后部署和配置应用程序。 项目可以是：

* 想要在 VM 上安装的工具，例如，代理、Fiddler 和 Visual Studio。
* 想要在 VM 上运行的操作，例如，克隆存储库。
* 想要测试的应用程序。

项目是 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) JSON 文件，包含执行部署和应用配置的说明。 

## <a name="artifact-repositories"></a>项目存储库
项目存储库是其中已签入项目的 Git 存储库。 可在组织中以启用重用和共享的形式向多个实验室添加相同的项目存储库。

## <a name="base-images"></a>基础映像
基础映像为 VM 映像，包含预安装和配置为快速创建 VM 的所有工具和设置。 可通过选取现有基并添加项目来对 VM 进行预配，进而安装测试代理。 然后可将预配的 VM 保存为基，这样无需为每个 VM 的预配重新安装测试代理即可使用该基。

## <a name="formulas"></a>公式
除基础映像外，还可使用公式提供用于快速预配 VM 的机制。 开发测试实验室中的公式是用于创建实验室 VM 的默认属性值的列表。 利用公式，可以创建具有相同属性（例如，基础映像、VM 大小、虚拟网络和项目）组的 VM，而无需每次都指定这些属性。 使用公式创建 VM 时，可以按原样使用默认值，也可以修改默认值。

## <a name="caps"></a>上限
上限是一种可最大程度减少实验室浪费的机制。 例如，可以将上限设置为限制每个用户或在实验室中可创建的 VM 数量。

## <a name="policies"></a>策略
策略有助于控制实验室中的成本。 例如，可以创建策略来自动关闭基于定义计划的 VM。

## <a name="security-levels"></a>安全级别
安全访问权限由 Azure 基于角色的访问控制 (RBAC) 决定。 若要了解如何访问，最好先了解权限、角色和由 RBAC 定义的作用域之间的差异。 

* 权限 - 权限是对特定操作的定义访问，例如，对所有虚拟机的读取访问权限。 
* 角色 - 角色是一组可进行分组、可分配给用户的权限。 例如，“订阅所有者”角色对订阅中所有资源都具有访问权限。 
* 作用域 - 作用域是 Azure 资源层次结构中的级别，例如，资源组、单个实验室或整个订阅。

开发测试实验室的作用域中存在两种定义用户权限的角色：实验室所有者和实验室用户。

* 实验室所有者 - 实验室所有者具有对实验室中所有资源的访问权限。 因此，实验室所有者可以修改策略、读取和写入任意 VM、更改虚拟网络等。 
* 实验室用户 - 实验室用户可查看实验室中的所有资源（例如 VM、策略和虚拟网络），但不能修改其他用户创建的策略和 VM。 

若要查看如何在开发测试实验室中创建自定义角色，可参阅本文[向用户授予特定实验室策略的权限](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。

由于作用域是分层的，因此用户在某个作用域具有权限时，将会自动授予他所包含的每个较低级别的作用域的权限。 例如，如果用户分配到订阅所有者的角色，那么他可访问订阅中的所有资源，包括所有虚拟机、所有虚拟网络和所有实验室。 因此，订阅所有者将自动继承实验室所有者的角色。 但是反过来则不适用。 实验室所有者具有访问实验室的权限，其低于订阅级别的作用域。 因此，实验室所有者将不能查看虚拟机或虚拟网络或实验室之外的任何资源。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
[在开发测试实验室中创建实验室](devtest-lab-create-lab.md)




<!--HONumber=Nov16_HO3-->


