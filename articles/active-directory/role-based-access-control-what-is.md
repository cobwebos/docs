---
title: "基于角色的访问控制 | Microsoft Docs"
description: "Azure 门户中具有 Azure 基于角色的访问控制的访问管理入门。 在目录中使用角色分配来分配权限。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0e724ac386e9b92904b6218e4fcc0ac1766378ce


---
# <a name="get-started-with-access-management-in-the-azure-portal"></a>Azure 门户中的访问管理入门
面向安全的公司应侧重于向员工提供他们所需的确切权限。 权限过多，攻击者可轻松威胁帐户。 权限太少，员工无法有效完成其工作。 Azure 基于角色的访问控制 (RBAC) 通过对 Azure 提供细致的访问管理帮助解决此问题。

使用 RBAC，你可以在团队中对职责进行分配，仅向用户授予执行作业所需的访问权限。 而不是向每个人提供对你的 Azure 订阅或资源的无限权限，你可以仅允许某些操作。 例如，使用 RBAC 允许一个员工管理订阅中的虚拟机，而允许另一个员工管理同一订阅中的 SQL 数据库。

## <a name="basics-of-access-management-in-azure"></a>Azure 中的访问管理的基础知识
每个 Azure 订阅都会与一个 Azure Active Directory (AD) 目录关联。 该目录中的用户、组和应用程序可以管理 Azure 订阅中的资源。 使用 Azure 门户、Azure 命令行工具或 Azure 管理 API 分配这些访问权限。

通过将相应的 RBAC 角色分配给特定范围内的用户、组和应用程序授予访问权限。 角色分配的范围可以是订阅、资源组或单个资源。 分配在父范围内的角色也会将访问权限授予给其中所含的子范围。 例如，具有对资源组访问权限的用户可以管理其包含的所有资源，如网站、虚拟机和子网。

![Azure Active Directory 元素之间的关系 - 关系图](./media/role-based-access-control-what-is/rbac_aad.png)

你分配的 RBAC 角色决定了用户、组或应用程序可以在该范围内所管理的资源。

## <a name="built-in-roles"></a>内置角色
Azure RBAC 有三种适用于所有资源类型的基本角色：

* **所有者**具有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。
* **参与者**可以创建和管理所有类型的 Azure 资源，但不能将访问权限授予其他用户。
* **读取器**可以查看现有 Azure 资源。

Azure 中的其他 RBAC 角色允许对特定的 Azure 资源进行管理。 例如，虚拟机参与者角色允许用户创建和管理虚拟机。 它并不授予其访问虚拟机连接的虚拟网络或子网的权限。

[RBAC 内置角色](role-based-access-built-in-roles.md)列出了 Azure 中可用的角色。 它指定每个内置角色向用户授予的操作和范围。 若要定义自己的角色以便进一步控制，请参阅如何生成 [Azure RBAC 中的自定义角色](role-based-access-control-custom-roles.md)。

## <a name="resource-hierarchy-and-access-inheritance"></a>资源层次结构和访问权限继承
* Azure 中的每个**订阅**仅属于一个目录。
* 每个**资源组**仅属于一个订阅。
* 每个**资源**仅属于一个资源组。

你在父范围授予的访问权限在子范围被继承。 例如：

* 将读取器角色分配给订阅范围内的 Azure AD 组。 该组成员可以查看订阅中的每个资源组和资源。
* 将参与者角色分配给资源组范围内的应用程序。 它可以管理该资源组中的所有类型的资源，但不能管理该订阅中的其他资源组资源。

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC 与经典订阅管理员
经典订阅管理员和共同管理员具有对 Azure 订阅的完全访问权限。 他们可以将 [Azure 门户](https://portal.azure.com)和 Azure Resource Manager API 配合使用或使用 [Azure 经典门户](https://manage.windowsazure.com) 和 Azure 经典部署模型管理资源。 在 RBAC 模型中，经典管理员具有订阅范围内的所有者角色。

仅 Azure 门户和新的 Azure Resource Manager API 支持 Azure RBAC。 分配了 RBAC 角色的用户和应用程序不能使用经典管理门户和 Azure 经典部署模型。

## <a name="authorization-for-management-vs-data-operations"></a>管理授权与数据操作
Azure RBAC 仅支持 Azure 门户和 Azure Resource Manager API 中的 Azure 资源的管理操作。 它不能授权 Azure 资源的所有数据级别操作。 例如，你可以授权某人管理存储帐户，但该用户不能管理存储帐户内的 blob 或表。 同样，可以管理SQL 数据库，但是不能管理其中的表。

## <a name="next-steps"></a>后续步骤
* [Azure 门户中基于角色的访问控制](role-based-access-control-configure.md)入门。
* 请参阅 [RBAC 内置角色](role-based-access-built-in-roles.md)
* 定义你在 [Azure RBAC 中的自定义角色](role-based-access-control-custom-roles.md)




<!--HONumber=Nov16_HO3-->


