---
title: Windows 虚拟桌面 PowerShell-Azure
description: 如何在设置 Windows 虚拟桌面租户环境时对 PowerShell 问题进行故障排除。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: de3933b1686b433a720e78d89bd3e50c0ce5e5dd
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615495"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows 虚拟桌面 PowerShell

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../troubleshoot-powershell.md)。

本文介绍了在 Windows 虚拟桌面中使用 PowerShell 时的错误和问题。 有关远程桌面服务 PowerShell 的详细信息，请参阅[Windows 虚拟桌面 PowerShell](/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 虚拟桌面安装过程中使用的 PowerShell 命令

本部分列出了在设置 Windows 虚拟桌面时通常使用的 PowerShell 命令，并提供解决使用这些命令时可能发生的问题的方法。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>错误： RdsAppGroupUser 命令-指定的 UserPrincipalName 已分配给指定主机池中的 RemoteApp 应用组

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**原因：** 使用的用户名已分配给不同类型的应用组。 不能将用户分配到同一会话主机池下的远程桌面和远程应用组。

**修复：** 如果用户需要远程应用和远程桌面，请创建不同的主机池或授予用户对远程桌面的访问权限，这将允许使用会话主机 VM 上的任何应用程序。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>错误： RdsAppGroupUser 命令-指定的 UserPrincipalName 在与远程桌面租户关联的 Azure Active Directory 中不存在

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**原因：** 在绑定到 Windows 虚拟桌面租户的 Azure Active Directory 中找不到由-UserPrincipalName 指定的用户。

**修复：** 确认以下列表中的项。

- 用户将同步到 Azure Active Directory。
- 用户未与企业对消费者（B2C）或企业到企业（B2B）的商业客户联系。
- Windows 虚拟桌面租户绑定到正确的 Azure Active Directory。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>错误： RdsDiagnosticActivities--用户无权查询管理服务

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因：** -TenantName 参数

**修复：** 发出带有-TenantName \<TenantName> 的 RdsDiagnosticActivities。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>错误： RdsDiagnosticActivities--用户无权查询管理服务

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**原因：** 使用-Deployment 开关。

**修复：** -部署交换机只能由部署管理员使用。 这些管理员通常是远程桌面服务/Windows 虚拟桌面团队的成员。 将-Deployment 开关替换为-TenantName \<TenantName>。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>错误： RdsRoleAssignment--用户无权查询管理服务

**原因1：** 所使用的帐户对租户没有远程桌面服务所有者权限。

**修复1：** 具有远程桌面服务所有者权限的用户需要执行角色分配。

**原因2：** 所使用的帐户具有远程桌面服务所有者权限，但不是租户 Azure Active Directory 的一部分，或者没有查询用户所在的 Azure Active Directory 的权限。

**修复2：** 具有 Active Directory 权限的用户需要执行该角色分配。

>[!Note]
>RdsRoleAssignment 无法向 Azure Active Directory （AD）中不存在的用户授予权限。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview-2019.md)。
- 若要在 Windows 虚拟桌面环境中创建租户和主机池时排查问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues-2019.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration-2019.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection-2019.md)。
- 若要解决远程桌面客户端的问题，请参阅[排查远程桌面客户端](../troubleshoot-client.md)问题
- 若要了解有关该服务的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup-2019.md)。
- 若要浏览疑难解答教程，请参阅[教程：排查资源管理器模板部署问题](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../../azure-resource-manager/templates/deployment-history.md)。
