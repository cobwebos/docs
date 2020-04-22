---
title: 在 Windows 虚拟桌面中委派访问 - Azure
description: 如何委派 Windows 虚拟桌面部署的管理功能，包括示例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91451ff3024a9a5019b3982b0e4471e2c4d80c74
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683912"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虚拟桌面中的委托访问

Windows 虚拟桌面具有委派的访问模型，允许您通过为其分配角色来定义特定用户允许其拥有的访问量。 角色分配有三个组件：安全主体、角色定义和范围。 Windows 虚拟桌面委派访问模型基于 Azure RBAC 模型。 要了解有关特定角色分配及其组件的详细信息，请参阅[Azure 基于角色的访问控制概述](../role-based-access-control/built-in-roles.md)。

Windows 虚拟桌面委派访问支持角色分配的每个元素的以下值：

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

Windows 虚拟桌面中的委派访问权限具有多个内置角色定义，您可以将其分配给用户和服务主体。

* RDS 所有者可以管理所有内容，包括访问资源。
* RDS 参与者可以管理所有内容，但不能访问资源。
* RDS 阅读器可以查看所有内容，但不能进行任何更改。
* RDS 操作员可以查看诊断活动。

## <a name="powershell-cmdlets-for-role-assignments"></a>用于角色分配的 PowerShell cmdlet

您可以运行以下 cmdlet 来创建、查看和删除角色分配：

* **获取 RdsRole 分配**显示角色分配的列表。
* **新 RdsRole 分配**创建新的角色分配。
* **删除 RdsRole 分配**将删除角色分配。

### <a name="accepted-parameters"></a>接受的参数

您可以使用以下参数修改基本三个 cmdlet：

* **AadTenantId**：指定服务主体是成员的 Azure 活动目录租户 ID。
* **AppGroup 名称**：远程桌面应用组的名称。
* **诊断**：指示诊断范围。 （必须与**基础结构**或**租户**参数配对。
* **主机池名称**：远程桌面主机池的名称。
* **基础结构**：指示基础结构范围。
* **角色定义名称**：分配给用户、组或应用的基于角色的远程桌面服务访问控制角色的名称。 （例如，远程桌面服务所有者、远程桌面服务读取器等。
* **服务器原则名称**：Azure 活动目录应用程序的名称。
* **签名名称**：用户的电子邮件地址或用户主体名称。
* **租户名称**：远程桌面租户的名称。

## <a name="next-steps"></a>后续步骤

有关每个角色可以使用的 PowerShell cmdlet 的更完整列表，请参阅[PowerShell 引用](/powershell/windows-virtual-desktop/overview)。

有关如何设置 Windows 虚拟桌面环境的指南，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
