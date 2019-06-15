---
title: 自定义 RDP 属性使用 PowerShell-Azure
description: 如何使用 PowerShell cmdlet 进行自定义 RDP 属性适用于 Windows 虚拟桌面。
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082646"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自定义主机池的远程桌面协议属性

自定义主机池的远程桌面协议 (RDP) 属性，如多监视器体验和音频重定向，让你提供最佳体验为您基于其需求的用户。 你可以自定义 RDP 属性中使用 Windows 虚拟桌面 **-CustomRdpProperty**中的参数**集 RdsHostPool** cmdlet。

请参阅[远程桌面 RDP 文件设置](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files)有关受支持的属性及其默认值的完整列表。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>添加或编辑单个自定义 RDP 属性

若要添加或编辑单个自定义 RDP 属性，请运行以下 PowerShell cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![PowerShell cmdlet 的屏幕截图，突出显示 Get RDSRemoteApp 具有名称和友好名称。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>添加或编辑多个自定义 RDP 属性

若要添加或编辑多个自定义 RDP 属性，通过提供自定义 RDP 属性之间用分号分隔的字符串形式运行以下 PowerShell cmdlet:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![PowerShell cmdlet 的屏幕截图，突出显示 Get RDSRemoteApp 具有名称和友好名称。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重置所有自定义 RDP 属性

您可以通过重置单个自定义 RDP 属性为其默认值中的说明[添加或编辑单个自定义 RDP 属性](#add-or-edit-a-single-custom-rdp-property)，或可以通过运行以下命令重置主机池的所有自定义 RDP 属性PowerShell cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![PowerShell cmdlet 的屏幕截图，突出显示 Get RDSRemoteApp 具有名称和友好名称。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>后续步骤

现在，你已自定义 RDP 属性给定的主机池，你可以登录到 Windows 虚拟桌面客户端以测试这些用户会话的一部分。 为此，请转到连接到 Windows 虚拟桌面操作方法：

- [从 Windows 10 和 Windows 7 连接](connect-windows-7-and-10.md)
- [从 Web 浏览器进行连接](connect-web.md)
