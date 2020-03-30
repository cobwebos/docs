---
title: 排除服务连接 Windows 虚拟桌面 - Azure
description: 在 Windows 虚拟桌面租户环境中设置客户端连接时，如何解决问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127457"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 虚拟桌面服务连接

使用本文可以解决 Windows 虚拟桌面客户端连接的问题。

## <a name="provide-feedback"></a>提供反馈

您可以向我们提供反馈，并与[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)的产品团队和其他活动社区成员讨论 Windows 虚拟桌面服务。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>用户连接，但未显示任何内容（无源）

用户可以启动远程桌面客户端并能够进行身份验证，但用户在 Web 发现源中看不到任何图标。

确认报告问题的用户已使用此命令行分配给应用程序组：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

确认用户是否使用正确的凭据登录。

如果使用 Web 客户端，请确认不存在缓存的凭据问题。

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 企业多会话虚拟机不响应

如果虚拟机未响应，并且无法通过 RDP 访问它，则需要通过检查主机状态来使用诊断功能进行故障排除。

要检查主机状态，请运行此 cmdlet：

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

如果主机状态为`NoHeartBeat`，则表示 VM 未响应，并且代理无法与 Windows 虚拟桌面服务通信。

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

您可以执行以下几项操作来修复 NoHeartBeat 状态。

### <a name="update-fslogix"></a>更新 FSLogix

如果您的 FSLogix 不是最新的版本，特别是如果它是 frxdrvvt.sys 的版本 2.9.7205.27375，则可能会导致死锁。 请务必将[FSLogix 更新到最新版本](https://go.microsoft.com/fwlink/?linkid=2084562)。

### <a name="disable-bgtaskregistrationmaintenancetask"></a>禁用 BgTask 注册维护任务

如果更新 FSLogix 不起作用，则问题可能是 BiSrv 组件在每周维护任务期间耗尽了系统资源。 使用以下两种方法之一禁用 BgTask 注册维护任务，从而暂时禁用维护任务：

- 转到"开始"菜单并搜索**任务计划程序**。 导航到**任务调度库** > **微软** > **Windows** > **代理基础结构**。 查找名为**BgTask 注册维护任务**的任务。 找到它后，右键单击它，然后从下拉菜单中选择 **"禁用**"。
- 以管理员身份打开命令行菜单并运行以下命令：
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面和升级跟踪的故障排除概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 要在 Windows 虚拟桌面环境中创建租户和主机池时解决问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虚拟桌面中配置虚拟机 （VM） 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 要解决在 Windows 虚拟桌面中使用 PowerShell 时的问题，请参阅[Windows 虚拟桌面电源外壳](troubleshoot-powershell.md)。
- 要完成疑难解答教程，请参阅[教程：解决资源管理器模板部署的疑难解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
