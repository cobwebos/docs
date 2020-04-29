---
title: 管理单元管理（预览）-Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元获得更精细的委派权限
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406466"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory 中的管理单元管理（预览版）

本文介绍 Azure Active Directory (Azure AD) 中的管理单元。 管理单元是一项 Azure AD 资源，可以是其他 Azure AD 资源的容器。 在此预览版本中，管理单元只能包含用户和组。

使用管理单元，你可以授予限制为你定义的部门、区域或其他部门的管理员权限。 可以使用管理单元将权限委派给区域管理员或以粒度级别设置策略。 例如，用户帐户管理员可以更新配置文件信息、重置密码以及仅在其管理单元中为用户分配许可证。

 例如，委派给区域支持专家的支持[人员管理员](directory-assign-admin-roles.md#helpdesk-administrator)角色，该角色限制为仅管理其支持区域中的用户。

## <a name="deployment-scenario"></a>部署方案

使用管理单元限制管理作用域对于由任何种类的独立分部组成的组织都非常有用。 假设有一家大型大学的示例，此大学由许多自治学校（商业、学校工程等）组成，每个学校都有一组 IT 管理员控制访问、管理用户并为学校设置策略。 中心管理员可以：

- 使用管理权限创建一个角色，该角色仅限商业学校管理单元中的 Azure AD 用户
- 为企业创建管理单元
- 仅限商业学校学生和员工填写管理单元
- 将业务学校 IT 团队添加到具有其作用域的角色

## <a name="license-requirements"></a>许可要求

使用管理单元需要为每个管理单元管理员 Azure Active Directory Premium 许可证，并 Azure Active Directory Free 管理单元成员的许可证。 有关详细信息，请参阅[Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)入门。

## <a name="manage-administrative-units"></a>对管理单元进行管理

在此预览版本中，你可以使用 Azure 门户、PowerShell cmdlet 和脚本或 Microsoft Graph 来管理管理单元。 有关详细信息，请参阅文档：

- [创建、删除、填充角色并将其添加到管理单元](roles-admin-units-manage.md)：完成操作方法过程
- 使用[管理单元](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)：如何使用 PowerShell 处理管理单元
- [管理单元图形支持](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)：有关管理单元 Microsoft Graph 的详细文档。

### <a name="planning-your-administrative-units"></a>规划管理单元

管理单元可用于对 Azure AD 资源进行逻辑分组。 例如，对于其 IT 部门在全球各地分散的组织而言，创建定义这些地理边界的管理单元可能有意义。 在另一种情况下，多国组织具有不同的 "子组织"，在运营中，每个子组织都可以由管理单元表示。

对于创建的管理单元，将根据组织的独特要求指导。 管理单元是在 M365 服务间定义结构的常用方法。 建议你在 M365 的服务中为你的管理单元做好使用。 可以在管理单元下的 M365 上关联常见资源时，从管理单元中获得最大价值。

你可能希望在组织中创建管理单元，以完成以下阶段：

1. 初始采用：你的组织将根据初始条件开始创建管理单元，并且随着条件的优化，管理单元数将增加。
1. 修剪：定义好条件后，将删除不再需要的管理单元。
1. 稳定：组织结构定义完善，并且管理单元的数量不会在短时间内大幅变化。

## <a name="currently-supported-scenarios"></a>当前支持的方案

"全局管理员" 或 "特权角色管理员" 可以使用 Azure AD 门户创建管理单元，将用户添加为管理单元的成员，然后将其分配给管理单元范围内的管理员角色。 然后，管理单元范围内的管理员可以使用 Office 365 门户来管理其管理单元中的用户的基本管理。

此外，可以将组作为管理单元的成员添加，管理单元范围的组管理员可以使用 PowerShell、Microsoft Graph 和 Azure AD 门户来管理这些组。

下表描述了对管理单元方案的当前支持。

### <a name="administrative-unit-management"></a>管理单元管理

权限 |   MS Graph/PowerShell   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
创建和删除管理单元   |    支持    |   支持   |    不支持
单独添加和删除管理单元成员    |   支持    |   支持   |    不支持
使用 .csv 文件批量添加和删除管理单元成员   |    不支持     |  支持   |    无计划支持
分配管理单元范围的管理员  |     支持    |   支持    |   不支持
基于特性动态添加和删除 AU 成员 | 不支持 | 不支持 | 不支持

### <a name="user-management"></a>用户管理

权限 |   MS Graph/PowerShell   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
管理单元范围的用户属性、密码和许可证管理   |    支持     |  支持   |   支持
管理单元范围阻止和取消阻止用户登录    |   支持   |    支持   |    支持
管理单元范围内的用户 MFA 凭据管理   |    支持   |   支持   |   不支持

### <a name="group-management"></a>组管理

权限 |   MS Graph/PowerShell   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
管理单元范围的组属性和成员管理     |  支持   |    支持    |  不支持
管理单元范围内的组许可管理   |    支持  |    支持   |   不支持

> [!NOTE]
>
> 具有管理单元范围的管理员不能管理动态组成员身份规则。

管理单元仅将作用域应用到管理权限。 它们不会阻止成员或管理员使用其[默认的用户权限](../fundamentals/users-default-permissions.md)来浏览管理单元以外的其他用户、组或资源。 在 Office 365 门户中，将筛选出作用域内管理员管理单元以外的用户，但你可以浏览 Azure AD 门户、PowerShell 和其他 Microsoft 服务中的其他用户。

## <a name="next-steps"></a>后续步骤

- [管理澳大利亚](roles-admin-units-manage.md)
- [在澳大利亚管理用户](roles-admin-units-add-manage-users.md)
- [在澳大利亚管理组](roles-admin-units-add-manage-groups.md)
- [将作用域内角色分配给 AU](roles-admin-units-assign-roles.md)