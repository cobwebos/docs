---
title: 用户、组、许可和角色概述 - Azure AD | Microsoft Docs
description: Azure Active Directory 中分配的用户和许可证、管理员角色与组成员身份之间的关系
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: vincesm
ms.date: 11/08/2019
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8052bb44df6d29e17304763051fa2099009b9701
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583021"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>大型组织的用户、组、许可和角色

本文为 Azure AD 管理员介绍最重要[标识管理](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context)任务在用户的组、许可证、已部署企业应用与管理员角色方面的关系。 随着组织的不断发展，可以使用 Azure AD 组和管理员角色来实现以下目的：

* 将许可证分配到组，而无需逐个分配
* 委托权限，以将 Azure AD 管理工作分配给特权更低的角色
* 将企业应用访问权限分配到组

## <a name="assign-users-to-groups"></a>将用户分配到组

可以使用 Azure AD 中的组将许可证分配给大量用户，或者为用户分配已部署企业应用的访问权限。 可以在 Azure AD 中使用组分配除全局管理员以外的所有管理员角色，也可以授予对外部资源（例如 SaaS 应用程序或 SharePoint 站点）的访问权限。

为了提高灵活性和减少管理组成员身份的工作量，可以使用 Azure AD 中的[动态组](groups-create-rule.md)来自动扩展和收缩组成员身份。 对于属于一个或多个动态组的每个独特用户，需要 Azure AD Premium P1 许可证。

## <a name="assign-licenses-to-groups"></a>将许可证分配到组

逐个地分配或删除用户的许可证可能非常耗费时间和精力。 如果[将许可证分配到组](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)，则可以更轻松地完成大规模许可证管理。

在 Azure AD 中，当用户加入许可的组时，他们会自动获得相应的许可证。 当用户离开组时，Azure AD 会删除其许可证分配。 如果不使用 Azure AD 组，则必须编写 PowerShell 脚本或使用图形 API 才能批量添加或删除加入或离开组织的用户的用户许可证。

如果没有足够的可用许可证或出现无法同时分配服务计划之类的问题，在 Azure 门户中可以看到组出现任何许可问题的状态。

>[!NOTE]
>基于组的许可功能目前以公共预览版提供。 在预览期，可在任何付费 Azure Active Directory (Azure AD) 许可计划或试用版中使用此功能。

## <a name="delegate-administrator-roles"></a>委托管理员角色

许多大型组织希望其用户能够通过某些选项获取足够的权限来完成其工作任务，而无需向必须注册应用程序的用户分配强大的全局管理员等角色。 下面是可以帮助你以更高的粒度分配应用程序管理工作的新 Azure AD 管理员角色示例：

 角色名称 | 权限摘要
 --------- | -------------------
 **应用程序管理员** | 可以添加和管理企业应用程序与应用程序注册，以及配置代理应用程序设置。 应用程序管理员可以查看条件访问策略和设备，但不能对其进行管理。
 **云应用程序管理员** | 可以添加和管理企业应用程序与企业应用注册。 此角色拥有应用程序管理员的所有权限，但不能管理应用程序代理设置。
**应用程序开发人员** | 可以添加和更新应用程序注册，但不能管理企业应用程序或配置应用程序代理。

我们正在添加新的 Azure AD 管理员角色。 请查看 Azure 门户或[管理员角色权限参考](directory-assign-admin-roles.md)来了解当前可用的角色。

## <a name="assign-app-access"></a>分配应用访问权限

可以使用 Azure AD 为组分配对 [Azure AD 组织中部署的企业应用](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)的访问权限。 如果在应用中结合使用动态组和组分配，则可以在组织不断发展的过程中，来自动分配用户对应用的访问权限。 需要一个 Azure Active Directory Premium P1 或 Premium P2 许可证才能分配对企业应用的访问权限。

在 Azure AD 中，还可以精细控制你有权访问的应用和组之间流动的数据。 在[企业应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)中打开一个应用，并选择“预配”以执行以下操作： 

* 为支持自动预配的应用设置自动预配
* 提供凭据以连接到应用的用户管理 API
* 设置映射，用于在预配或更新用户帐户时，控制对 Azure AD 与应用之间的流产生影响的用户
* 启动和停止应用的 Azure AD 预配服务、清除预配缓存，或重启服务
* 查看“预配活动报告”，其中提供了在 Azure AD 与应用之间创建、更新和删除的所有用户与组的日志；以及查看“预配错误报告”，其中提供了更详细的错误消息  

## <a name="next-steps"></a>后续步骤

入门级的 Azure AD 管理员可在 [Azure Active Directory 基础知识](https://docs.microsoft.com/azure/active-directory/fundamentals/index)中获取基础知识。

或者，可以开始[创建组](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)、[分配许可证](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)、[分配应用访问权限](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context)或[分配管理员角色](directory-assign-admin-roles.md)。
