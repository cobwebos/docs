---
title: 通过 PowerShell 自定义 RDP 属性-Azure
description: 如何通过 PowerShell cmdlet 为 Windows 虚拟桌面自定义 RDP 属性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: helohr
ms.openlocfilehash: 624edaea9a0fb56e34eb83f033dfdab64985bd5c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950710"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自定义主机池远程桌面协议属性

通过自定义主机池的远程桌面协议 (RDP) 属性 (例如多监视器体验和音频重定向), 可以根据用户的需要为用户提供最佳体验。 你可以使用**RdsHostPool** cmdlet 中的 **-CustomRdpProperty**参数自定义 Windows 虚拟桌面中的 RDP 属性。

有关支持的属性及其默认值的完整列表, 请参阅[远程桌面 RDP 文件设置](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files)。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>添加或编辑单个自定义 RDP 属性

若要添加或编辑单个自定义 RDP 属性, 请运行以下 PowerShell cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>添加或编辑多个自定义 RDP 属性

若要添加或编辑多个自定义 RDP 属性, 请运行以下 PowerShell cmdlet, 方法是将自定义 RDP 属性提供为分号分隔的字符串:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重置所有自定义 RDP 属性

可以按照[添加或编辑单个自定义 rdp 属性](#add-or-edit-a-single-custom-rdp-property)中的说明, 将各个自定义 rdp 属性重置为其默认值, 也可以通过运行以下 PowerShell cmdlet 来重置主机池的所有自定义 rdp 属性:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>后续步骤

自定义给定主机池的 RDP 属性后, 可以登录到 Windows 虚拟桌面客户端, 将其作为用户会话的一部分进行测试。 为此, 请继续阅读连接到 Windows 虚拟桌面的操作指南:

- [从 Windows 10 和 Windows 7 连接](connect-windows-7-and-10.md)
- [从 Web 浏览器进行连接](connect-web.md)
