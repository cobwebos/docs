---
title: 通过 PowerShell Windows 虚拟桌面 (经典) 自定义 RDP 属性-Azure
description: 如何通过 PowerShell cmdlet 为 Windows 虚拟桌面 (经典) 自定义 RDP 属性。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 260125b5e7aa4537c6e204d93263b65be042793a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540738"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>为 Windows 虚拟桌面 (经典) 主机池自定义远程桌面协议属性

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../customize-rdp-properties.md)。

通过自定义主机池的远程桌面协议 (RDP) 属性（例如多监视器体验和音频重定向），可以根据用户的需要为用户提供最佳体验。 你可以使用**RdsHostPool** cmdlet 中的 **-CustomRdpProperty**参数自定义 Windows 虚拟桌面中的 RDP 属性。

有关支持的属性及其默认值的完整列表，请参阅 [支持的 RDP 文件设置](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) 。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>默认 RDP 属性

默认情况下，已发布的 RDP 文件包含以下属性：

|RDP 属性 | 台式机 | RemoteApps |
|---|---| --- |
| 多监视器模式 | 已启用 | 空值 |
| 已启用驱动器重定向 | 驱动器、剪贴板、打印机、COM 端口、USB 设备和智能卡| 驱动器、剪贴板和打印机 |
| 远程音频模式 | 本地播放 | 本地播放 |

你为主机池定义的任何自定义属性都将覆盖这些默认值。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>添加或编辑单个自定义 RDP 属性

若要添加或编辑单个自定义 RDP 属性，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![已突出显示名称和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图，以编辑自定义的 R D P 属性。](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>添加或编辑多个自定义 RDP 属性

若要添加或编辑多个自定义 RDP 属性，请运行以下 PowerShell cmdlet，方法是将自定义 RDP 属性提供为分号分隔的字符串：

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![已突出显示名称和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图，以编辑自定义的 R D P 属性。](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重置所有自定义 RDP 属性

可以按照 [添加或编辑单个自定义 rdp 属性](#add-or-edit-a-single-custom-rdp-property)中的说明，将各个自定义 rdp 属性重置为其默认值，也可以通过运行以下 PowerShell cmdlet 来重置主机池的所有自定义 rdp 属性：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>后续步骤

自定义给定主机池的 RDP 属性后，可以登录到 Windows 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 接下来的两篇操作指南将会介绍如何使用所选的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](connect-windows-7-10-2019.md)
- [使用 Web 客户端进行连接](connect-web-2019.md)
