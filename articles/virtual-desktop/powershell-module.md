---
title: PowerShell 模块 Windows 虚拟桌面-Azure
description: 如何安装和设置适用于 Windows 虚拟桌面的 PowerShell 模块。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6741c034351099f544c20749eb7c7a39e7932181
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195131"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>设置适用于 Windows 虚拟桌面的 PowerShell 模块

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 2020 春季更新版。
>
> Windows 虚拟桌面 2020 春季更新版目前为公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虚拟桌面 PowerShell 模块已集成到 Azure PowerShell 模块中。 本文介绍如何设置 PowerShell 模块，以便可以运行 Windows 虚拟桌面的 cmdlet。

## <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境

若要开始使用该模块，请首先安装[最新版本的 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)。 Windows 虚拟桌面 cmdlet 目前仅适用于 PowerShell Core。

接下来，需要安装要在 PowerShell 会话中使用的 DesktopVirtualization 模块。

在提升模式下运行以下 PowerShell cmdlet 以安装模块：

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> 如果此 cmdlet 不起作用，请尝试使用提升的权限再次运行它。

接下来，运行以下 cmdlet 以连接到 Azure：

```powershell
Connect-AzAccount
```

登录到 Azure 帐户需要在运行 Connect cmdlet 时生成的代码。 若要登录，请 <https://microsoft.com/devicelogin> 使用 Azure 管理员凭据登录，然后输入代码。

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

这会将你直接登录到你的管理员凭据的默认订阅。

## <a name="change-the-default-subscription"></a>更改默认订阅

如果要在登录后更改默认订阅，请运行以下 cmdlet：

```powershell
Select-AzSubscription -SubscriptionName <preferredsubscriptionname>
```

选择要使用的新订阅时，无需在以后运行的 cmdlet 中指定该订阅的 ID。 例如，以下 cmdlet 检索特定的会话主机，而无需订阅 ID：

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

还可以通过将所需的订阅名称作为参数添加来更改每个 cmdlet 的订阅。 下一个 cmdlet 与前面的示例相同，不同之处在于订阅 ID 作为参数添加，用于更改 cmdlet 使用的订阅。

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>获取位置

对于创建新对象的所有**AzWVD** cmdlet，location 参数是必需的。

运行以下 cmdlet 以获取订阅支持的位置列表：

```powershell
Get-AzLocation
```

**AzLocation**的输出将如下所示：

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

知道帐户的位置后，可以在 cmdlet 中使用它。 例如，以下是在 "southeastasia" 位置创建主机池的 cmdlet：

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>后续步骤

设置 PowerShell 模块后，可以运行 cmdlet 来完成 Windows 虚拟桌面中的各种操作。 下面是一些可以使用模块的位置：

- 运行[Windows 虚拟桌面教程]()，设置自己的 Windows 虚拟桌面环境。
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)
- [配置 Windows 虚拟桌面负载均衡方法](configure-host-pool-load-balancing.md)
- [配置个人桌面主机池分配类型](configure-host-pool-personal-desktop-assignment-type.md)
- 还有更多！

如果遇到任何问题，请查看[PowerShell 故障排除文章](troubleshoot-powershell.md)以获取帮助。

