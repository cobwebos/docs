---
title: 使用 Azure 管理组来组织资源 | Microsoft Docs
description: 了解管理组以及如何使用它们。
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/31/2018
ms.author: rithorn
ms.openlocfilehash: edc57d146ccb034ac3fd627386000a1953b0e558
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480316"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>使用 Azure 管理组来组织资源

如果你的组织有多个订阅，则可能需要一种方法来高效地管理这些订阅的访问权限、策略和符合性。 Azure 管理组提供订阅上的作用域级别。 可将订阅组织到名为“管理组”的容器中，并将管理条件应用到管理组。 管理组中的所有订阅都将自动继承应用于管理组的条件。 不管使用什么类型的订阅，管理组都能提供大规模的企业级管理。

例如，可将策略应用到限制创建虚拟机 (VM) 的区域的管理组。 此策略将应用到该管理组下面的所有管理组、订阅和资源，只允许在该区域中创建 VM。

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>管理组和订阅的层次结构

可以生成管理组和订阅的灵活层次结构，以便将资源组织成用于统一策略和访问管理的层次结构。
下图显示了使用管理组创建用于调控的层次结构的示例。

![树](media/management-groups/MG_overview.png)

按此示例所示创建层次结构可对“基础结构团队管理组”应用策略（例如，将 VM 位置限制为美国西部区域），以启用内部符合性与安全策略。 此策略将继承到该管理组下的两个 EA 订阅，并应用到这些订阅下的所有 VM。 由于此策略从管理组继承到订阅，因此，资源或订阅所有者无法更改此安全策略，以改善调控能力。

使用管理组的另一个场景是向用户提供对多个订阅的访问权限。  通过移动该管理组下的多个订阅，可在该管理组中创建一个 RBAC 分配，该分配将这种访问权限继承到所有订阅。  无需基于多个订阅编写 RBAC 分配的脚本，管理组中的一个分配就能让用户访问所需的一切内容。

### <a name="important-facts-about-management-groups"></a>关于管理组的重要事实

- 在单个目录中可以支持 10,000 个管理组。
- 一个管理组树最多可支持六个级别的深度。
  - 此限制不包括根级别或订阅级别。
- 每个管理组和订阅只能支持一个父级。
- 每个管理组可以包含多个子级。
- 所有订阅和管理组都包含在每个目录中的单个层次结构内。 有关预览版期间的异常，请参阅[关于根管理组的重要事实](#important-facts-about-the-root-management-group)。

## <a name="root-management-group-for-each-directory"></a>每个目录的根管理组

为每个目录指定了一个称为“根”管理组的顶级管理组。 此根管理组内置在层次结构中，包含其所有下级管理组和订阅。 此根管理组允许在目录级别应用全局策略和 RBAC 分配。 [目录管理员最初需要提升自身的权限](../role-based-access-control/elevate-access-global-admin.md)才能成为此根组的所有者。 当管理员成为组的所有者后，可将任何 RBAC 角色分配给其他目录用户或组来管理层次结构。  

### <a name="important-facts-about-the-root-management-group"></a>关于根管理组的重要事实

- 默认情况下，已提供根管理组的名称和 ID。 随时可以更新此显示名称，以便在 Azure 门户中显示其他名称。
  - 该名称将为“租户根组”。
  - ID 将为 Azure Active Directory ID。
- 无法像操作其他管理组一样移动或删除根管理组。  
- 所有订阅和管理组归并到目录中的一个根管理组下。
  - 目录中的所有资源归并到根管理组进行全局管理。
  - 新订阅在创建时自动默认为根管理组。
- 所有 Azure 客户都可查看根管理组，但并非所有客户都具有管理该根管理组的权限。
  - 有权访问订阅的每个人都可看到订阅位于层次结构中的位置的上下文。  
  - 未对任何人授予对根管理组的默认访问权限。 只有目录全局管理员可将自身提升为拥有访问权限的角色。  拥有访问权限后，目录管理员可向要管理的其他用户分配任何 RBAC 角色。  

>[!NOTE]
>如果你的目录在 2018 年 6 月 25 日之前开始使用管理组服务，则你的目录可能未在层次结构中包含所有订阅。 管理组团队正在以追溯方式更新在 2018 年 7 月/8 月之前开始在公共预览中使用管理组的每个目录。 目录中的所有订阅都将成为根管理组下的子级。  
>
>如果你有关于此追溯过程的问题，请联系：managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>管理组的初始设置

任何用户都需在开始使用管理组时进行初始设置。 第一步是在目录中创建根管理组。 创建此组后，目录中存在的所有现有订阅都成为根管理组的子级。  执行此过程是为了确保一个目录中只有一个管理组层次结构。  目录中的单个层次结构可让管理客户应用目录内其他客户无法绕开的全局访问权限和策略。 通过在目录中包含一个层次结构，在根上分配的任何内容都适用于该目录中的所有管理组、订阅、资源组和资源。  

> [!IMPORTANT]
> 对根管理组进行的任何用户访问权限分配或策略分配都适用于在目录中的所有资源。 因此，所有客户都应评估在此作用域中定义项目的需求。  用户访问权限和策略分配应仅在此作用域内“必须拥有”。  
  
## <a name="management-group-access"></a>访问管理组

Azure 管理组支持使用 [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 来访问所有资源访问和定义角色。 层次结构中的子资源继承这些权限。 可将任何内置 RBAC 角色分配到管理组，该角色将继承资源的层次结构。  例如，可以向管理组分配 RBAC 角色 VM 参与者。 此角色不对管理组执行任何操作，但将继承该管理组下的所有 VM。  

下图列出了管理组的角色和支持的操作。

| RBAC 角色名称             | 创建 | 重命名 | 移动 | 删除 | 分配访问权限 | 分配策略 | 读取  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|所有者                       | X      | X      | X    | X      | X             | X             | X     |
|参与者                 | X      | X      | X    | X      |               |               | X     |
|MG 参与者*             | X      | X      | X    | X      |               |               | X     |
|读取器                      |        |        |      |        |               |               | X     |
|MG 读取者*                  |        |        |      |        |               |               | X     |
|资源策略参与者 |        |        |      |        |               | X             |       |
|用户访问管理员   |        |        |      |        | X             |               |       |

*：MG 参与者和 MG 读取者只允许用户在管理组范围执行这些操作。  

### <a name="custom-rbac-role-definition-and-assignment"></a>自定义 RBAC 角色定义和分配

管理组当前不支持自定义 RBAC 角色。  请访问[管理组反馈论坛](https://aka.ms/mgfeedback)，查看此项的状态。

## <a name="next-steps"></a>后续步骤

若要了解有关管理组的详细信息，请参阅：

- [创建管理组来组织 Azure 资源](management-groups-create.md)
- [如何更改、删除或管理管理组](management-groups-manage.md)
- [安装 Azure Powershell 模块](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [查看 REST API 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [安装 Azure CLI 扩展](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
