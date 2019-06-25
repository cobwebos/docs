---
title: 在 Windows 虚拟桌面预览-Azure 中的委托的访问
description: 如何委托上的 Windows 虚拟桌面预览部署，包括示例的管理功能。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 41cf5f8bcc69e181350a63d215fb0d78d43dcfdf
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272814"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>在 Windows 虚拟桌面预览中的委托的访问

Windows 虚拟桌面预览提供了委托的访问模式，允许您定义的特定用户可通过将其分配一个角色拥有的访问权限。 角色分配有三个组件： 安全主体、 角色定义和作用域。 Windows 虚拟桌面的委托的访问模型基于 Azure RBAC 模型。 若要了解有关特定的角色分配和其组件的详细信息，请参阅[Azure 基于角色的访问控制概述](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)。

Windows 虚拟机委派访问支持的角色分配每个元素的以下值：

* 安全主体
    * 用户
    * 服务主体
* 角色定义
    * 内置角色
* 范围
    * 租户组
    * 租户
    * 主机池
    * 应用组

## <a name="built-in-roles"></a>内置角色

在 Windows 虚拟桌面中的委派的访问具有几个内置角色定义，可以将分配给用户和服务主体。

* RDS 所有者可以管理一切，包括对资源的访问。
* RDS 参与者可以管理资源的访问权限以外的所有内容。
* RDS 读取器可以查看所有内容，但不能进行任何更改。
* RDS 操作员可以查看诊断活动。

## <a name="powershell-cmdlets-for-role-assignments"></a>角色分配的 PowerShell cmdlet

可以运行以下 cmdlet 以创建、 查看和删除角色分配：

* **Get RdsRoleAssignment**显示的角色分配列表。
* **新 RdsRoleAssignment**创建新的角色分配。
* **删除 RdsRoleAssignment**删除角色分配。

### <a name="accepted-parameters"></a>已接受的参数

您可以修改基本的三个 cmdlet 使用以下参数：

* **AadTenantId**： 指定从其服务主体是其成员的 Azure Active Directory 租户 ID。
* **AppGroupName**： 远程桌面应用程序组的名称。
* **诊断**： 指示诊断作用域。 (必须与任何一个成对**基础结构**或**租户**参数。)
* **HostPoolName**： 远程桌面主机池的名称。
* **基础结构**： 指示基础结构范围。
* **RoleDefinitionName**： 分配给用户、 组或应用程序的远程桌面服务基于角色的访问控制角色的名称。 （例如，远程桌面服务所有者、 远程桌面服务读取器等。）
* **ServerPrincipleName**: Azure Active Directory 应用程序的名称。
* **SignInName**： 用户的电子邮件地址或用户主体名称。
* **TenantName**： 远程桌面租户的名称。

## <a name="next-steps"></a>后续步骤

每个角色可以使用 PowerShell cmdlet 的更完整列表，请参阅[PowerShell 参考](/powershell/windows-virtual-desktop/overview)。

有关如何设置 Windows 虚拟桌面环境的指导，请参阅[Windows 虚拟桌面预览环境](environment-setup.md)。
