---
title: "使用 Azure 管理组来组织资源 | Microsoft Docs"
description: "了解管理组以及如何使用它们。"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: rithorn
ms.openlocfilehash: 1264bf77b6d922f5beb22177d1ac63efa9386ef2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>使用 Azure 管理组组织资源 

如果你有多个订阅，则可以将它们组织成名为“管理组”的容器，以帮助你跨订阅管理访问、策略和合规性。 不管使用什么类型的订阅，管理组都能提供大规模的企业级管理。  

管理组功能目前以公共预览版提供。 若要开始使用管理组，请登录到 [Azure 门户](https://portal.azure.com)，在“所有服务”部分搜索“管理组”。 

管理组的 Azure 策略支持尚未在公共预览版中提供，在今后几周之内即将推出。  

例如，可将策略应用到限制创建虚拟机 (VM) 的区域的管理组。 此策略将应用到该管理组下面的所有管理组、订阅和资源，只允许在该区域中创建 VM。

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>管理组和订阅的层次结构 

可以生成管理组和订阅的灵活层次结构，以便将资源组织成用于统一策略和访问管理的层次结构。 下图显示的示例层次结构由按部门组织的管理组和订阅构成。    

![层次结构](media/management-groups/MG_overview.png)

创建按部门分组的层次结构后，可以分配由该管理组下的部门继承的 [Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 角色。 使用管理组时，只需分配角色一次，因此可以减少工作负荷，并减少出错的风险。 

### <a name="important-facts-about-management-groups"></a>关于管理组的重要事实
- 在单个目录中可以支持 10,000 个管理组。 
- 一个管理组树最多可支持六个级别的深度。
    - 此限制不包括根级别或订阅级别。
- 每个管理组只能支持一个父级。
- 每个管理组可以包含多个子级。 

## <a name="root-management-group-for-each-directory"></a>每个目录的根管理组

为每个目录指定了一个称为“根”管理组的顶级管理组。 此根管理组内置在层次结构中，包含其所有下级管理组和订阅。 此根管理组允许在目录级别应用全局策略和 RBAC 分配。 [目录管理员最初需要提升自身的权限](../active-directory/role-based-access-control-tenant-admin-access.md)才能成为此根组的所有者。 当管理员成为组的所有者后，可将任何 RBAC 角色分配给其他目录用户或组来管理层次结构。  

### <a name="important-facts-about-the-root-management-group"></a>关于根管理组的重要事实
- 默认情况下，根管理组的名称和 ID 是 Azure Active Directory 的 ID。 随时可以更新此显示名称，以便在 Azure 门户中显示其他名称。 
- 所有订阅和管理组可以归并到目录中的一个根管理组下。  
    - 建议将目录中的所有项归并到根管理组以进行全局管理。  
    - 在公共预览版中，目录中的所有订阅不会自动变成根的子级。   
    - 在公共预览版中，新订阅不会自动默认为根管理组。 
- 无法像操作其他管理组一样移动或删除根管理组。 
  
## <a name="management-group-access"></a>访问管理组

Azure 管理组支持使用 [Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 来访问所有资源访问和定义角色。 层次结构中的子资源继承这些权限。   

可将任何[内置 RBAC 角色](../active-directory/role-based-access-control-what-is.md#built-in-roles)分配到管理组，不过，通常会使用四个角色： 
- **所有者**具有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 
- 参与者可以创建和管理所有类型的 Azure 资源，但不能将访问权限授予其他用户。
- **资源策略参与者**可以在资源的目录中创建和管理策略。     
- **读取器**可以查看现有 Azure 资源。 


## <a name="next-steps"></a>后续步骤 
若要了解有关管理组的详细信息，请参阅： 
- [创建管理组来组织 Azure 资源](management-groups-create.md)
- [如何更改、删除或管理管理组](management-groups-manage.md)
- [安装 Azure Powershell 模块](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [查看 REST API 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [安装 Azure CLI 扩展](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

