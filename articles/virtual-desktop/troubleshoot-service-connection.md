---
title: 排查服务连接问题 Windows 虚拟桌面-Azure
description: 如何解决在 Windows 虚拟桌面租户环境中设置客户端连接时遇到的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: bf1af6ab0d8187452ecc2e48dcf72de1093690fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477104"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 虚拟桌面服务连接

使用本文解决 Windows 虚拟桌面客户端连接问题。

## <a name="provide-feedback"></a>提供反馈

你可以向我们提供反馈，并与[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)中的产品团队和其他活动社区成员讨论 Windows 虚拟桌面服务。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>用户连接，但不显示任何内容（无源）

用户可以启动远程桌面客户端，并可以进行身份验证，但用户看不到 web 发现源中的任何图标。

使用以下命令行确认已将报告问题的用户分配给应用程序组：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

确认用户正在用正确的凭据登录。

如果正在使用 web 客户端，请确认没有缓存的凭据问题。

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 企业多会话虚拟机未响应

如果虚拟机未响应并且不能通过 RDP 访问它，则需要通过检查主机状态来使用诊断功能对其进行故障排除。

若要检查主机状态，请运行以下 cmdlet：

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

如果主机状态为 "`NoHeartBeat`"，则表示 VM 未响应并且代理无法与 Windows 虚拟桌面服务通信。

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
```

可以执行几项操作来修复 NoHeartBeat 状态。

### <a name="update-fslogix"></a>更新 FSLogix

如果 FSLogix 不是最新的，尤其是2.9.7205.27375 版本的 frxdrvvt 时，这可能会导致死锁。 请确保将[FSLogix 更新为最新版本](https://go.microsoft.com/fwlink/?linkid=2084562)。

### <a name="disable-bgtaskregistrationmaintenancetask"></a>禁用 BgTaskRegistrationMaintenanceTask

如果更新 FSLogix 不起作用，则问题可能是每周维护任务期间 BiSrv 组件耗尽系统资源。 通过使用以下两种方法之一禁用 BgTaskRegistrationMaintenanceTask 来暂时禁用维护任务：

- 请在 "开始" 菜单中搜索**任务计划程序**。 导航到**任务计划程序库** > **Microsoft** > **Windows** > **BrokerInfrastructure**。 查找名为**BgTaskRegistrationMaintenanceTask**的任务。 找到该文件后，右键单击该文件，然后从下拉菜单中选择 "**禁用**"。
- 以管理员身份打开命令行菜单，并运行以下命令：
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建租户和主机池时排查问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要浏览疑难解答教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/resource-manager-tutorial-troubleshoot.md)。
