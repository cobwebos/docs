---
title: Windows 虚拟桌面 PowerShell-Azure
description: 如何对 PowerShell 的问题进行故障排除时设置的 Windows 虚拟桌面租户环境。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 06b955365ffc7c0a1dff93db95932d8696293e9f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605246"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows 虚拟桌面 PowerShell

使用此文章使用 PowerShell 管理 Windows 虚拟桌面时解决错误和问题。 远程桌面服务 PowerShell 的详细信息，请参阅[Windows 虚拟桌面 Powershell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供反馈

目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 虚拟桌面安装过程中使用的 PowerShell 命令

本部分列出了 PowerShell 命令，通常用于设置 Windows 虚拟桌面时，并提供方法来解决在使用它们时可能出现的问题。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>错误：添加 RdsAppGroupUser 命令-指定的 UserPrincipalName 已分配到 RemoteApp 应用程序组中指定的主机池

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

原因：  使用的用户名具有已分配给不同类型的应用程序组。 无法将用户分配到同一个会话主机池下的远程桌面和远程应用程序组。

**解决方法：** 如果用户需要远程应用和远程桌面时，创建不同的主机池或授予对远程桌面会话主机 VM 将允许使用任何应用程序的用户访问权限。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>错误：添加 RdsAppGroupUser 命令-指定的 UserPrincipalName 与远程桌面的 tenant 关联的 Azure Active Directory 中不存在

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

原因：  绑定到 Windows 虚拟桌面租户在 Azure Active Directory 中找不到指定的 UserPrincipalName 的用户。

**解决方法：** 确认以下列表中的项。

- 用户同步到 Azure Active Directory 中。
- 用户不能局限于企业对消费者 (B2C) 或企业到企业 (B2B) 商务。
- Windows 虚拟桌面租户被绑定到正确的 Azure Active Directory。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>错误：Get-RdsDiagnosticActivities-用户不是有权查询管理服务

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因：** -TenantName 参数

**解决方法：** 发出 Get RdsDiagnosticActivities 与-TenantName <TenantName>。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>错误：Get-RdsDiagnosticActivities-用户不是有权查询管理服务

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

原因：  使用-部署开关。

**修复：** -部署开关可仅由部署管理员。 这些管理员通常是远程桌面服务/Windows 虚拟桌面团队的成员。 替换为与-TenantName-部署开关<TenantName>。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>错误：新 RdsRoleAssignment-用户不是有权查询管理服务

**原因 1：** 所使用的帐户没有远程桌面服务所有者权限的租户。

**修复 1:** 具有远程桌面服务所有者权限的用户需要执行的角色分配。

**原因 2：** 所使用的帐户具有远程桌面服务的所有者权限，但不是租户的 Azure Active Directory 的一部分或没有权限来查询 Azure Active Directory 用户所在的位置。

**修复 2:** 具有 Active Directory 权限的用户需要执行的角色分配。

>[!Note]
>新 RdsRoleAssignment 无法向不存在 Azure Active Directory (AD) 中的用户授予权限。

## <a name="next-steps"></a>后续步骤

- 有关故障排除 Windows 虚拟桌面，升级进行跟踪的概述，请参阅[故障排除概述、 反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建的租户和主机池时排查问题，请参阅[租户以及主机池创建](troubleshoot-set-up-issues.md)。
- 若要在 Windows 虚拟机中配置虚拟机 (VM) 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要对 Windows 虚拟桌面客户端连接的问题进行故障排除，请参阅[远程桌面客户端连接](troubleshoot-client-connection.md)。
- 若要了解有关预览服务的详细信息，请参阅[Windows Desktop 预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要完成故障排除教程，请参阅[教程：对资源管理器模板部署进行故障排除](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。