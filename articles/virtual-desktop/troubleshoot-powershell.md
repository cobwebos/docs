---
title: Windows 虚拟桌面电源外壳 - Azure
description: 在设置 Windows 虚拟桌面租户环境时，如何解决 PowerShell 的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127463"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows 虚拟桌面 PowerShell

使用本文可以解决在 Windows 虚拟桌面中使用 PowerShell 时的错误和问题。 有关远程桌面服务 PowerShell 的详细信息，请参阅[Windows 虚拟桌面电源外壳](/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>在 Windows 虚拟桌面设置期间使用的 PowerShell 命令

本节列出了在设置 Windows 虚拟桌面时通常使用的 PowerShell 命令，并提供了解决使用时可能出现的问题的方法。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>错误：Add-RdsAppGroupUser 命令 -- 指定的用户主体名称已分配给指定主机池中的 RemoteApp 应用组

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**原因：** 使用的用户名已分配给不同类型的应用组。 无法将用户分配给同一会话主机池下的远程桌面和远程应用组。

**修复：** 如果用户同时需要远程应用和远程桌面，请创建不同的主机池或授予用户对远程桌面的访问权限，这将允许在会话主机 VM 上使用任何应用程序。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>错误：添加 RdsAppGroupUser 命令 -- 与远程桌面租户关联的 Azure 活动目录中不存在指定的用户主体名称

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**原因：**-UserTheName 指定的用户无法在绑定到 Windows 虚拟桌面租户的 Azure 活动目录中找到。

**修复：** 确认以下列表中的项目。

- 用户同步到 Azure 活动目录。
- 用户不与企业 （B2C） 或企业对企业 （B2B） 商务相关联。
- Windows 虚拟桌面租户绑定到正确的 Azure 活动目录。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>错误：获取诊断活动 -- 用户无权查询管理服务

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因：** -租户名称参数

**修复：** 使用 -租户名称\<租户名称>发出获取诊断活动的问题。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>错误：获取诊断活动 -- 用户无权查询管理服务

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**原因：** 使用 -部署开关。

**修复：-** 部署交换机只能由部署管理员使用。 这些管理员通常是远程桌面服务/Windows 虚拟桌面团队的成员。 将 -部署开关替换为 -租户\<名称租户名称>。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>错误：新 RdSRole 分配 -- 用户无权查询管理服务

**原因 1：** 正在使用的帐户对租户没有远程桌面服务所有者权限。

**修复 1：** 具有远程桌面服务所有者权限的用户需要执行角色分配。

**原因 2：** 正在使用的帐户具有远程桌面服务所有者权限，但不是租户的 Azure 活动目录的一部分，或者没有查询用户所在的 Azure 活动目录的权限。

**修复 2：** 具有活动目录权限的用户需要执行角色分配。

>[!Note]
>New-RdsRole 分配不能向 Azure 活动目录 （AD） 中不存在的用户授予权限。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面和升级跟踪的故障排除概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 要在 Windows 虚拟桌面环境中创建租户和主机池时解决问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虚拟桌面中配置虚拟机 （VM） 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 要解决 Windows 虚拟桌面客户端连接的问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 要解决远程桌面客户端的问题，请参阅[排除远程桌面客户端的故障](troubleshoot-client.md)
- 要了解有关该服务的更多信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 要完成疑难解答教程，请参阅[教程：解决资源管理器模板部署的疑难解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
