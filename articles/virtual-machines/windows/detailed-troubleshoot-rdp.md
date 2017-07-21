---
title: "在 Azure 中对远程桌面进行详细故障排除 | Microsoft Docs"
description: "查看针对无法连接到 Azure 中的 Windows 虚拟机时出现的远程桌面错误提供的详细故障排除步骤"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: "无法连接到远程桌面, 远程桌面故障排除, 远程桌面无法连接, 远程桌面错误, 远程桌面故障排除, 远程桌面问题"
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: support-article
ms.date: 05/31/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: bd832efd3902dd61be1a8b64396eaadf1cd76e24
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017


---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Azure 中 Windows VM 远程桌面连接问题的详细故障排除步骤
本文提供详细的故障排除步骤，用于为基于 Windows 的 Azure 虚拟机诊断和修复复杂的远程桌面错误。

> [!IMPORTANT]
> 若要消除更常见的远程桌面错误，请务必先阅读[远程桌面的基本故障排除文章](troubleshoot-rdp-connection.md)，然后再继续。

你可能遇到不像[基本远程桌面故障排除指南](troubleshoot-rdp-connection.md)中所述的任何特定错误消息的远程桌面错误消息。 请遵循以下步骤来确定远程桌面 (RDP) 客户端为何无法连接到 Azure VM 上的 RDP 服务。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

如果你对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并单击“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

## <a name="components-of-a-remote-desktop-connection"></a>远程桌面连接的组件
RDP 连接涉及以下组件：

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

在继续之前，在脑海中回想一下自上次远程桌面成功连接到 VM 后更改的内容可能会有帮助。 例如：

* VM 或包含 VM 的云服务的公共 IP 地址（也称为虚拟 IP 地址 ([VIP](https://en.wikipedia.org/wiki/Virtual_IP_address))）已更改。 RDP 失败的原因可能是 DNS 客户端缓存仍然有针对该 DNS 名称注册的*旧 IP 地址*。 请刷新 DNS 客户端缓存，并重新尝试连接 VM。 或者尝试直接使用新 VIP 进行连接。
* 使用第三方应用程序来管理远程桌面连接，而不是使用 Azure 门户所生成的连接。 验证应用程序配置是否包含正确的远程桌面流量 TCP 端口。 可以通过在 [Azure 门户](https://portal.azure.com)中单击 VM 的“设置”>“终结点”来检查经典虚拟机的此端口。

## <a name="preliminary-steps"></a>预备步骤
在继续进行详细故障排除之前，

* 在 Azure 门户中检查虚拟机的状态，查看是否存在任何明显问题。
* 按照[基本故障排除指南中常见 RDP 错误的快速修复步骤](troubleshoot-rdp-connection.md#quick-troubleshooting-steps)操作

在执行这些步骤后，尝试通过远程桌面重新连接到 VM。

## <a name="detailed-troubleshooting-steps"></a>详细故障排除步骤
由于以下源出现问题，远程桌面客户端可能无法访问 Azure VM 上的远程桌面服务：

* [远程桌面客户端计算机](#source-1-remote-desktop-client-computer)
* [组织 Intranet 边缘设备](#source-2-organization-intranet-edge-device)
* [云服务终结点和访问控制列表 (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [网络安全组](#source-4-network-security-groups)
* [基于 Windows 的 Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>来源 1：远程桌面客户端计算机
验证你的计算机是否可以与本地另一台基于 Windows 的计算机建立远程桌面连接。

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

如果不能，请检查你的计算机上是否有以下设置：

* 阻止远程桌面流量的本地防火墙设置。
* 本地安装的阻止远程桌面连接的客户端代理软件。
* 本地安装的阻止远程桌面连接的网络监视软件。
* 其他类型的阻止远程桌面连接的安全软件，该软件监视流量或允许/禁止特定类型的流量。

对于所有这些情况，请暂时禁用可疑软件，然后尝试通过远程桌面连接到本地计算机。 如果你可以通过这种方式找出实际原因，请与网络管理员协作，更正软件设置，以允许远程桌面连接。

## <a name="source-2-organization-intranet-edge-device"></a>来源 2：组织 Intranet 边缘设备
验证直接连接到 Internet 的计算机是否可以与 Azure 虚拟机建立远程桌面连接。

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

如果没有直接连接到 Internet 的计算机，则可以在资源组或云服务中创建新的 Azure 虚拟机并使用它进行测试。 有关详细信息，请参阅[在 Azure 中创建运行 Windows 的虚拟机](../virtual-machines-windows-hero-tutorial.md)。 在测试后，可以删除该虚拟机和资源组或云服务。

如果你可以创建与直接连接到 Internet 的计算机的远程桌面连接，请检查你组织的 Intranet 边缘设备中是否有以下项：

* 阻止与 Internet 的 HTTPS 连接的内部防火墙。
* 阻止远程桌面连接的代理服务器。
* 边界网络中的设备上运行的阻止远程桌面连接的入侵检测或网络监视软件。

与网络管理员协作，更正组织 Intranet 边缘设备的设置，以允许与 Internet 建立基于 HTTPS 的远程桌面连接。

## <a name="source-3-cloud-service-endpoint-and-acl"></a>来源 3：云服务终结点和 ACL
对于使用经典部署模型创建的 VM，请验证位于同一云服务或虚拟网络中的另一个 Azure VM 是否可以与 Azure VM 建立远程桌面连接。

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> 对于在 Resource Manager 中创建的虚拟机，请转到[来源 4：网络安全组](#source-4-network-security-groups)。

如果同一云服务或虚拟网络中没有其他虚拟机，请创建一个。 遵循[在 Azure 中创建运行 Windows 的虚拟机](../virtual-machines-windows-hero-tutorial.md)中所述的步骤。 测试完成后，请删除测试虚拟机。

如果可以通过远程桌面连接到同一云服务或虚拟网络中的虚拟机，请检查以下设置：

* 目标 VM 上远程桌面通信的终结点配置：终结点的专用 TCP 端口必须与 VM 的远程桌面服务正在侦听的 TCP 端口（默认值为 3389）匹配。
* 目标 VM 上远程桌面通信终结点的 ACL：ACL 允许你指定基于源 IP 地址允许或拒绝从 Internet 传入的流量。 错误配置 ACL 可能会阻止传入远程桌面流量到达终结点。 检查你的 ACL 以确保允许从你的代理服务器或其他边缘服务器的公共 IP 地址传入的流量。 有关详细信息，请参阅[什么是网络访问控制列表 (ACL)？](../../virtual-network/virtual-networks-acl.md)

若要检查终结点是否是问题的源，请删除当前终结点，然后创建一个新终结点，并选择范围 49152-65535 中的随机端口作为外部端口号。 有关详细信息，请参阅[如何对虚拟机设置终结点](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="source-4-network-security-groups"></a>来源 4：网络安全组
使用网络安全组可以对允许的入站和出站流量进行更精细的控制。 你可以创建跨 Azure 虚拟网络中的子网和云服务的规则。 检查你的网络安全组规则，以确保允许来自 Internet 的远程桌面流量：

* 在 Azure 门户中，选择你的 VM
* 单击“所有设置” | “网络接口”，然后选择你的网络接口。
* 单击“所有设置” | “网络安全组”，然后选择你的网络安全组。
* 单击“所有设置” | “入站安全规则”，并确保 TCP 端口 3389 上有允许 RDP 的规则。
  * 如果尚无规则，请单击“添加”创建规则。 输入 **TCP** 作为协议，然后输入 **3389** 作为目标端口范围。
  * 确保将操作设置为“允许”，然后单击“确定”保存新的入站规则。

有关详细信息，请参阅 [什么是网络安全组 (NSG)？](../../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>来源 5：基于 Windows 的 Azure VM
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

请按[此文](reset-rdp.md)说明执行操作。 此文介绍如何在虚拟机上重置远程桌面服务：

* 启用“远程桌面”Windows 防火墙默认规则（TCP 端口 3389）。
* 通过将 HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections 注册表值设置为 0，启用远程桌面连接。

从你的计算机重试连接。 如果仍无法通过远程桌面连接，请检查是否存在以下可能问题：

* “远程桌面”服务未在目标 VM 上运行。
* “远程桌面”服务未侦听 TCP 端口 3389。
* Windows 防火墙或其他本地防火墙使用阻止远程桌面通信的出站规则。
* Azure 虚拟机上运行的入侵检测或网络监视软件正在阻止远程桌面连接。

对于使用经典部署模型创建的 VM，可以使用与 Azure 虚拟机的远程 Azure PowerShell 会话。 首先，需要安装虚拟机托管云服务的证书。 转到[为 Azure 虚拟机配置安全远程 PowerShell 访问](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)，将 **InstallWinRMCertAzureVM.ps1** 脚本文件下载到本地计算机。

接下来，安装 Azure PowerShell（如果尚未安装）。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

接下来，打开 Azure PowerShell 命令提示符，将当前文件夹更改为 **InstallWinRMCertAzureVM.ps1** 脚本文件所在的位置。 若要运行 Azure PowerShell 脚本，必须设置正确的执行策略。 运行 **Get-ExecutionPolicy** 命令，确定当前的策略级别。 有关设置相应级别的信息，请参阅 [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)。

接下来，填写你的 Azure 订阅名称、云服务名称和虚拟机名称（删除 < 和 > 字符），然后运行这些命令。

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

可以从 **Get-AzureSubscription** 命令显示的 *SubscriptionName* 属性获取正确的订阅名称。 可以从 **Get-AzureVM** 命令显示的 *ServiceName* 列中获取虚拟机的云服务名称。

检查是否有新证书。 打开当前用户的“证书”管理单元，然后在“受信任的根证书颁发机构\证书”文件夹中查找。 你应看到在“颁发给”列中具有你的云服务的 DNS 名称的证书（示例：cloudservice4testing.cloudapp.net）。

接下来，使用以下命令启动远程 Azure PowerShell 会话。

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

输入有效的管理员凭据之后，应会看到类似于下面的 Azure PowerShell 提示：

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

此提示的第一部分是云服务名称（其中包含目标 VM），这可能不同于“cloudservice4testing.cloudapp.net”。 现在，可以对此云服务发出 Azure PowerShell 命令，调查上述问题，并更正配置。

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>手动更正远程桌面服务侦听 TCP 端口
出现远程 Azure PowerShell 会话提示时，运行此命令。

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

PortNumber 属性显示当前端口号。 如果需要，可使用此命令将远程桌面端口号更改回其默认值 (3389)。

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

使用此命令验证是否已将端口更改为 3389。

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

使用此命令退出远程 Azure PowerShell 会话。

```powershell
Exit-PSSession
```

验证 Azure VM 的远程桌面终结点是否也使用 TCP 端口 3398 作为其内部端口。 重启 Azure VM，并重新尝试远程桌面连接。

## <a name="additional-resources"></a>其他资源
[如何为 Windows 虚拟机重置密码或远程桌面服务](reset-rdp.md)

[如何安装和配置 Azure PowerShell](/powershell/azure/overview)

[对于基于 Linux 的 Azure 虚拟机的 Secure Shell (SSH) 连接进行故障排除](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[对在 Azure 虚拟机上运行的应用程序的访问进行故障排除](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


