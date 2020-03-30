---
title: 使用电源外壳自定义 RDP 属性 - Azure
description: 如何使用 PowerShell cmdlet 自定义 Windows 虚拟桌面的 RDP 属性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128063"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>为主机池自定义远程桌面协议属性

自定义主机池的远程桌面协议 （RDP） 属性（如多监视器体验和音频重定向）可让您根据用户的需求为用户提供最佳体验。 您可以使用**Set-RdsHostPool** cmdlet 中的 **-CustomRdp属性**参数自定义 Windows 虚拟桌面中的 RDP 属性。

有关受支持属性及其默认值的完整列表，请参阅[支持的 RDP 文件设置](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>默认 RDP 属性

默认情况下，已发布的 RDP 文件包含以下属性：

|RDP 属性 | 台式机 | RemoteApps |
|---|---| --- |
| 多显示器模式 | 已启用 | 空值 |
| 启用驱动器重定向 | 驱动器、剪贴板、打印机、COM 端口、USB 设备和智能卡| 驱动器、剪贴板和打印机 |
| 远程音频模式 | 在本地播放 | 在本地播放 |

为主机池定义的任何自定义属性都将覆盖这些默认值。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>添加或编辑单个自定义 RDP 属性

要添加或编辑单个自定义 RDP 属性，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![突出显示了 PowerShell cmdlet get-RDSRemoteApp 的屏幕截图，突出显示了名称和友好名称。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>添加或编辑多个自定义 RDP 属性

要添加或编辑多个自定义 RDP 属性，请通过提供自定义 RDP 属性作为分号分隔字符串运行以下 PowerShell cmdlet：

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![突出显示了 PowerShell cmdlet get-RDSRemoteApp 的屏幕截图，突出显示了名称和友好名称。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重置所有自定义 RDP 属性

您可以按照["添加"或编辑单个自定义 RDP 属性](#add-or-edit-a-single-custom-rdp-property)中的说明将单个自定义 RDP 属性重置为其默认值，也可以通过运行以下 PowerShell cmdlet 来重置主机池的所有自定义 RDP 属性：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![突出显示了 PowerShell cmdlet get-RDSRemoteApp 的屏幕截图，突出显示了名称和友好名称。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>后续步骤

现在，您已为给定的主机池自定义了 RDP 属性，可以登录到 Windows 虚拟桌面客户端以作为用户会话的一部分对其进行测试。 接下来的两个"如何"将告诉您如何使用您选择的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](connect-windows-7-and-10.md)
- [使用 Web 客户端进行连接](connect-web.md)
