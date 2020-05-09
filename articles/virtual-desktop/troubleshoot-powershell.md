---
title: Windows 虚拟桌面 PowerShell-Azure
description: 如何在设置 Windows 虚拟桌面环境时排查 PowerShell 问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce19c670df5062a11bf86e9c383a322f9033818d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612004"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows 虚拟桌面 PowerShell

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍了在 Windows 虚拟桌面中使用 PowerShell 时的错误和问题。 有关远程桌面服务 PowerShell 的详细信息，请参阅[Windows 虚拟桌面 PowerShell](/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 虚拟桌面安装过程中使用的 PowerShell 命令

本部分列出了在设置 Windows 虚拟桌面时通常使用的 PowerShell 命令，并提供解决使用这些命令时可能发生的问题的方法。

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>错误： AzRoleAssignment：提供的信息未映射到 AD 对象 ID

```powershell
AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

**原因：** 在绑定到 Windows 虚拟桌面环境的 Azure Active Directory 中找不到由 *-SignInName*参数指定的用户。 

**修复：** 请确保以下各项。

- 应将用户同步到 Azure Active Directory。
- 该用户不应与企业到消费者（B2C）或企业对企业（B2B）的商业客户联系。
- Windows 虚拟桌面环境应绑定到正确的 Azure Active Directory。

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>错误： AzRoleAssignment： "具有对象 id 的客户端无权对作用域执行操作（代码： AuthorizationFailed）"

**原因1：** 正在使用的帐户不具有对订阅的所有者权限。 

**修复1：** 具有所有者权限的用户需要执行角色分配。 或者，需要将用户分配到 "用户访问管理员" 角色，以将用户分配到应用程序组。

**原因2：** 使用的帐户具有 "所有者" 权限，但不是环境的 Azure Active Directory 的一部分，或者不具有查询用户所在 Azure Active Directory 的权限。

**修复2：** 具有 Active Directory 权限的用户需要执行该角色分配。

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>错误： AzWvdHostPool--位置不可用于资源类型

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'. 
```

原因： Windows 虚拟桌面支持选择主机池、应用程序组和工作区的位置，以将服务元数据存储在某些位置。 你的选项仅限于此功能可用的位置。 此错误表示该功能在所选位置中不可用。

修复：在错误消息中，将发布受支持区域的列表。 改为使用受支持的区域之一。

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>错误： AzWvdApplicationGroup 必须与主机池位于同一位置

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**原因：** 位置不匹配。 所有主机池、应用程序组和工作区都有一个存储服务元数据的位置。 你创建的与彼此关联的任何对象都必须位于同一位置。 例如，如果主机池处于中`eastus`，则还需要在中`eastus`创建应用程序组。 如果创建工作区以便将这些应用程序组注册到，则该工作区也`eastus`需要位于中。

**修复：** 检索创建主机池的位置，然后将创建的应用程序组分配到该位置。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要解决设置 Windows 虚拟桌面环境和主机池时遇到的问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要解决远程桌面客户端的问题，请参阅[排查远程桌面客户端](troubleshoot-client.md)问题
- 若要了解有关该服务的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
