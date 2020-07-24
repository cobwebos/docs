---
title: 排查服务连接问题 Windows 虚拟桌面秋季 2019-Azure
description: 如何解决在 Windows 虚拟桌面租户环境中设置客户端连接时遇到的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad5e740a2874d398f3947fe755024bedc897ec73
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085786"
---
# <a name="troubleshoot-windows-virtual-desktop-service-connections"></a>Windows 虚拟桌面服务连接疑难解答

>[!IMPORTANT]
>本教程的内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的 2019 年秋季版。 如果要尝试管理 2020 年春季版更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../troubleshoot-service-connection.md)。

使用本文解决 Windows 虚拟桌面客户端连接问题。

## <a name="provide-feedback"></a>提供反馈

可以通过 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，向我们提供反馈并与产品团队和其他活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>用户可以连接，但不显示任何内容（无源）

用户可以启动远程桌面客户端，也可以进行身份验证，但在 Web 发现源中看不到任何图标。

使用以下命令行确认已将报告问题的用户分配给应用程序组：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

确认用户正在用正确的凭据登录。

如果正在使用 Web 客户端，请确认没有缓存的凭据问题。

## <a name="next-steps"></a>后续步骤

- 如需简要了解如何排查 Windows 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview-2019.md)。
- 若要排查在 Windows 虚拟桌面环境中创建租户和主机池时遇到的问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues-2019.md)。
- 若要排查在 Windows 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration-2019.md)。
- 若要排查将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅 [Windows 虚拟桌面 PowerShell](troubleshoot-powershell-2019.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
