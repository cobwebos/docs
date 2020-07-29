---
title: 排查服务连接问题 Windows 虚拟桌面（经典）-Azure
description: 如何解决在 Windows 虚拟桌面（经典）租户环境中设置客户端连接时遇到的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 213f7843e4896f8c7c9234e2c94433afbed1d565
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292517"
---
# <a name="windows-virtual-desktop-classic-service-connections"></a>Windows 虚拟桌面（经典）服务连接

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面（经典）。 如果尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../troubleshoot-service-connection.md)。

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
