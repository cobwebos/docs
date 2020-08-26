---
title: 管理单元管理（预览版）- Azure AD | Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元获得更精细的委派权限
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 093f4f3cd54f1e4638a0fbcd161a2af4fecba5da
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475781"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory 中的管理单元管理（预览）

本文介绍 Azure Active Directory (Azure AD) 中的管理单元。 管理单元是一种 Azure AD 资源，它可以是其他 Azure AD 资源的容器。 在此预览版本中，管理单元只能包含用户和组。

管理单元允许你授予仅限于你定义的部门、区域或组织的其他部门的管理权限。 你能够使用管理单元将权限委派给区域管理员或以粒度级别设置策略。 例如，用户帐户管理员可以更新配置文件信息、重置密码，并仅为其管理单元中的用户分配许可证。

 例如，委派给区域支持专家[支持管理员](directory-assign-admin-roles.md#helpdesk-administrator)角色，该角色限制为仅管理其支持的区域中的用户。

## <a name="deployment-scenario"></a>部署场景

在由任何类型的独立部门组成的组织中，使用管理单元限制管理范围非常有用。 以一所由许多自主学院（商学院、工程学院等）组成的大型大学为例，每个学院都有 IT 管理员团队控制访问权限、管理用户，以及为学院设置策略。 中心管理员可以：

- 创建具有仅对商学院管理单元中的 Azure AD 用户具有管理权限的角色
- 为商学院创建管理单元
- 仅可将商学院学生和员工填充到该管理单元
- 将商学院 IT 团队及其范围添加到角色中

## <a name="license-requirements"></a>许可要求

使用管理单元要求每个管理单元管理员具有 Azure Active Directory Premium 许可证，要求管理单元成员具有 Azure Active Directory Free 许可证。 有关详细信息，请参阅 [Azure AD Premium 入门](../fundamentals/active-directory-get-started-premium.md)。

## <a name="manage-administrative-units"></a>对管理单元进行管理

在此预览版中，可以使用 Azure 门户、PowerShell cmdlet 和脚本或 Microsoft Graph 对管理单元进行管理。 请参阅我们的文档获取详细信息：

- [创建、删除、填充角色并将其添加到管理单元](roles-admin-units-manage.md)：完成操作指南过程
- [处理管理单元](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)：如何使用 PowerShell 处理管理单元
- [管理单元 Graph 支持](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)：有关适用于管理单元的 Microsoft Graph 的详细文档。

### <a name="planning-your-administrative-units"></a>规划管理单元

管理单元可用于对 Azure AD 资源进行逻辑分组。 例如，对于 IT 部门分散在世界各地的组织而言，创建定义这些地理边界的管理单元可能有意义。 在另一种情况下，如果一个跨国组织具有不同的“子组织”，这些子组织的运营是半自主的，那么每个子组织可以由一个管理单元表示。

创建管理单元的条件将遵循组织的独特要求。 管理单元是跨 M365 服务定义结构的常用方法。 建议你在准备管理单元时考虑它们在各项 M365 服务中的使用。 如果可以在管理单元下跨 M365 关联共有资源，则可以通过管理单元获取最大价值。

组织中管理单元的创建会经历以下阶段：

1. 初始采用：组织将开始基于初始条件创建管理单元，并且随着条件的优化，管理单元的数量将增加。
1. 删除：一旦条件定义良好，将删除不再需要的管理单元。
1. 稳定化：组织结构定义良好，管理单元的数量不会在短时间内发生重大变化。

## <a name="currently-supported-scenarios"></a>当前支持的场景

全局管理员或特权角色管理员可以使用 Azure AD 门户创建管理单元，将用户添加为管理单元的成员，然后将 IT 人员分配给管理单元范围管理员角色。 然后，管理单元范围管理员可以使用 Office 365 门户对其管理单元中的用户进行基本管理。

此外，组可以添加为管理单元的成员，并且管理单元范围组管理员可以使用 PowerShell、Microsoft Graph 和 Azure AD 门户管理它们。

下表描述了对管理单元场景的当前支持。

### <a name="administrative-unit-management"></a>管理单元管理

权限 |   MS Graph/PowerShell   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
创建和删除管理单元   |    支持    |   支持   |    不支持
单独添加和删除管理单位成员    |   支持    |   支持   |    不支持
使用 .csv 文件批量添加和删除管理单元成员   |    不支持     |  支持   |    无支持计划
分配管理单元范围管理员  |     支持    |   支持    |   不支持
基于属性动态添加和删除 AU 成员 | 不支持 | 不支持 | 不支持

### <a name="user-management"></a>用户管理

权限 |   MS Graph/PowerShell   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
用户属性、密码、许可证的管理单元范围管理   |    支持     |  支持   |   支持
用户登录的管理单元范围阻止和取消阻止    |   支持   |    支持   |    支持
用户 MFA 凭据的管理单元范围管理   |    支持   |   支持   |   不支持

### <a name="group-management"></a>组管理

权限 |   MS Graph/PowerShell   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
组属性和成员的管理单元范围管理     |  支持   |    支持    |  不支持
组许可的管理单元范围管理   |    支持  |    支持   |   不支持

> [!NOTE]
>
> 具有管理单元范围的管理员无法管理动态组成员身份规则。

管理单元仅对管理权限应用范围。 它们不会阻止成员或管理员使用其[默认用户权限](../fundamentals/users-default-permissions.md)浏览管理单元外部的其他用户、组或资源。 在 Office 365 门户中，范围管理员的管理单元以外的用户会被筛选出，但你可以在 Azure AD 门户、PowerShell 和其他 Microsoft 服务中浏览其他用户。

## <a name="next-steps"></a>后续步骤

- [管理 AU](roles-admin-units-manage.md)
- [管理 AU 中的用户](roles-admin-units-add-manage-users.md)
- [管理 AU 中的组](roles-admin-units-add-manage-groups.md)
- [将范围角色分配给 AU](roles-admin-units-assign-roles.md)