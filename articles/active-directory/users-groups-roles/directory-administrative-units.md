---
title: 管理单位管理（预览） - Azure AD |微软文档
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406466"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory 中的管理单元管理（预览版）

本文介绍 Azure Active Directory (Azure AD) 中的管理单元。 管理单元是一项 Azure AD 资源，可以是其他 Azure AD 资源的容器。 在此预览版本中，管理单位只能包含用户和组。

管理单位允许您授予仅限于您定义的部门、区域或其他组织部门的管理员权限。 可以使用管理单元将权限委派给区域管理员或以粒度级别设置策略。 例如，用户帐户管理员可以仅更新其管理单元中的用户的配置文件信息、重置密码和为其分配许可证。

 例如，将[帮助台管理员](directory-assign-admin-roles.md#helpdesk-administrator)角色委派给区域支持专家，仅限于管理他们支持的区域的用户。

## <a name="deployment-scenario"></a>部署方案

在由任何类型的独立部门组成的组织中，使用行政单位限制管理范围是有用的。 以一所由许多自治学校（商学院、工程学院等）组成的大型大学为例，每所学校都有一支 IT 管理员团队，负责控制访问、管理用户和为其学校设置策略。 中央管理员可以：

- 创建具有管理权限的角色，仅对商学院管理单元中的 Azure AD 用户
- 为商学院创建管理单位
- 仅使用商学院学生和员工填充管理单元
- 将商学院 IT 团队添加到具有其范围的角色

## <a name="license-requirements"></a>许可要求

使用管理单元需要为每个管理单元管理员提供 Azure 活动目录高级许可证，并且对于管理单位成员的 Azure 活动目录免费许可证。 有关详细信息，请参阅使用[Azure AD 高级版入门](../fundamentals/active-directory-get-started-premium.md)。

## <a name="manage-administrative-units"></a>对管理单元进行管理

在此预览版本中，可以使用 Azure 门户、PowerShell cmdlet 和脚本或 Microsoft 图形管理管理单位。 详情请参阅我们的文档：

- [创建、删除、填充角色并将其添加到管理单位](roles-admin-units-manage.md)：完成操作过程
- [使用管理单位](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)：如何使用 PowerShell 使用管理单位
- [管理单位图支持](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)：有关管理单元的 Microsoft 图形的详细文档。

### <a name="planning-your-administrative-units"></a>规划管理单位

管理单位可用于逻辑地对 Azure AD 资源进行分组。 例如，对于 IT 部门分散于全球的组织，创建定义这些地理边界的管理单位可能有意义。 在另一种情况下，多国组织具有不同的"子组织"，在业务中是半自治的，每个子组织可能由一个行政单位代表。

创建行政单位的标准将以组织的独特要求为指导。 管理单元是定义 M365 服务结构的常见方法。 我们建议您在准备管理单元时，在 M365 服务中使用它们。 当可以在管理单位下跨 M365 关联公共资源时，可以从管理单位获取最大值。

您可以期望组织中创建管理单位将经历以下阶段：

1. 初始采用：您的组织将开始根据初始条件创建管理单位，并且随着条件的细化，管理单位的数量将会增加。
1. 修剪：一旦条件定义明确，将删除不再需要的管理单位。
1. 稳定：您的组织结构定义良好，行政单位的数量不会在短期内发生重大变化。

## <a name="currently-supported-scenarios"></a>当前支持的方案

全局管理员或特权角色管理员可以使用 Azure AD 门户创建管理单位，将用户添加为管理单位的成员，然后将 IT 人员分配给管理单位范围的管理员角色。 然后，管理单位范围的管理员可以使用 Office 365 门户对其管理单元中的用户进行基本管理。

此外，组可以添加为管理单位的成员，并且管理单位范围的组管理员可以使用 PowerShell、Microsoft 图形和 Azure AD 门户管理它们。

下表描述了对管理单元方案的当前支持。

### <a name="administrative-unit-management"></a>行政单位管理

权限 |   MS 图形/电源外壳   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
创建和删除管理单位   |    支持    |   支持   |    不支持
单独添加和删除管理单位成员    |   支持    |   支持   |    不支持
使用 .csv 文件批量添加和删除管理单位成员   |    不支持     |  支持   |    没有支持计划
分配管理单位范围的管理员  |     支持    |   支持    |   不支持
根据属性动态添加和删除 AU 成员 | 不支持 | 不支持 | 不支持

### <a name="user-management"></a>用户管理

权限 |   MS 图形/电源外壳   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
管理单位范围管理用户属性、密码、许可证   |    支持     |  支持   |   支持
管理单位范围的阻止和用户登录的取消阻止    |   支持   |    支持   |    支持
管理单位范围管理用户 MFA 凭据   |    支持   |   支持   |   不支持

### <a name="group-management"></a>组管理

权限 |   MS 图形/电源外壳   | Azure AD 门户 | Microsoft 365 管理中心
----------- | ----------------------- | --------------- | -----------------
组属性和成员的管理单位范围管理     |  支持   |    支持    |  不支持
集团许可管理单位范围管理   |    支持  |    支持   |   不支持

> [!NOTE]
>
> 具有管理单位作用域的管理员无法管理动态组成员身份规则。

管理单位仅将作用域应用于管理权限。 它们不会阻止成员或管理员使用其[默认用户权限](../fundamentals/users-default-permissions.md)浏览管理单位以外的其他用户、组或资源。 在 Office 365 门户中，将筛选出作用域管理员管理单位以外的用户，但您可以在 Azure AD 门户、PowerShell 和其他 Microsoft 服务中浏览其他用户。

## <a name="next-steps"></a>后续步骤

- [管理 A](roles-admin-units-manage.md)
- [管理 A 中的用户](roles-admin-units-add-manage-users.md)
- [管理 A 中的组](roles-admin-units-add-manage-groups.md)
- [将作用域角色分配给 AU](roles-admin-units-assign-roles.md)