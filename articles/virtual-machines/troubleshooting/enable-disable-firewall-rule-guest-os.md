---
title: 在 Azure VM 上的来宾 OS 中启用或禁用防火墙规则 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: cb2c548a94a91fe9126f684e382e9626adb93dd6
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319056"
---
# <a name="enable-or-disable-a-firewall-rule-on-a-azure-vm-guest-os"></a>在 Azure VM 来宾 OS 中启用或禁用防火墙规则

本文为排查以下问题提供参考：你怀疑来宾操作系统防火墙正在筛选虚拟机 (VM) 上的部分流量。 使用这些参考信息的原因如下：

*   如果有意对防火墙所做的更改导致 RDP 连接失败，使用“自定义脚本扩展”功能可以解决问题。

*   与设置特定于 RDP 的防火墙规则相比，禁用所有防火墙配置文件是更安全可靠的故障排除方法。

## <a name="solution"></a>解决方案

如何配置防火墙规则取决于对所需 VM 的访问级别。 以下示例使用 RDP 规则。 但是，可以通过指向正确的注册表项，将相同的方法应用于其他任何类型的流量。

### <a name="online-troubleshooting"></a>联机故障排除 

#### <a name="mitigation-1-custom-script-extension"></a>缓解措施 1：自定义脚本扩展

1.  使用以下模板创建脚本。

    *   启用规则：
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   禁用规则：
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  使用[自定义脚本扩展](../extensions/custom-script-windows.md)功能在 Azure 门户中上传此脚本。 

#### <a name="mitigation-2-remote-powershell"></a>缓解措施 2：远程 PowerShell

如果该 VM 处于联机状态且可以在同一虚拟网络中的另一个 VM 上对其进行访问，则可以使用另一个 VM 执行以下缓解措施。

1.  在故障排除 VM 上，打开 PowerShell 控制台窗口。

2.  根据情况运行以下命令。

    *   启用规则：
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   禁用规则：
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>缓解措施 3：PSTools 命令

如果 VM 处于联机状态且可以在同一虚拟网络中的另一个 VM 上对其进行访问，则可以使用另一个 VM 执行以下缓解措施。

1.  在故障排除 VM 上，下载 [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)。

2.  打开 CMD 实例，然后通过 VM 的内部 IP (DIP) 访问该 VM。 

    * 启用规则：
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   禁用规则：
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>缓解措施 4：远程注册表

如果 VM 处于联机状态且可以在同一虚拟网络中的另一个 VM 上对其进行访问，则可以在另一个 VM 上使用[远程注册表](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)。

1.  在故障排除 VM 上启动注册表编辑器 (regedit.exe)，然后选择“文件” > “连接网络注册表”。

2.  打开  *TARGET MACHINE*\SYSTEM 分支，然后指定以下值：

    * 若要启用规则，请打开以下注册表值：
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        然后，将字符串中的 **Active=FALSE** 更改为 **Active=TRUE**：

        **v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=@FirewallAPI.dll,-28775|Desc=@FirewallAPI.dll,-28756|EmbedCtxt=@FirewallAPI.dll,-28752|**
    
    * 若要禁用规则，请打开以下注册表值：
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        然后，将 **Active=TRUE** 更改为 **Active=FALSE**：
        
        **v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=@FirewallAPI.dll,-28775|Desc=@FirewallAPI.dll,-28756|EmbedCtxt=@FirewallAPI.dll,-28752|**

3.  重启 VM 以应用更改。

### <a name="offline-troubleshooting"></a>脱机故障排除 

如果无法通过任何方法访问该 VM，则无法使用自定义脚本扩展，此时，必须直接通过系统磁盘在脱机模式下工作。

在执行这些步骤之前，请创建受影响 VM 的系统磁盘的快照作为备份。 有关详细信息，请参阅 [创建磁盘快照](../windows/snapshot-copy-managed-disk.md)。

1.  [将系统磁盘附加到恢复 VM](troubleshoot-recovery-disks-portal-windows.md)。

2.  开始与恢复 VM 建立远程桌面连接。

3.  确保磁盘在磁盘管理控制台中标记为“联机”。 ****  请注意分配给附加的系统磁盘的驱动器号。

4.  在进行任何更改之前，请创建 \windows\system32\config 文件夹的副本，以防需要回滚更改。

5.  在故障排除 VM 上，启动注册表编辑器 (regedit.exe)。

6.  突出显示“HKEY_LOCAL_MACHINE”项，然后从菜单中选择“文件” > “加载配置单元”。  **** 

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  找到并打开 \windows\system32\config\SYSTEM 文件。 

    > [!Note]
    > 系统会提示输入名称。 输入  **BROKENSYSTEM**，然后展开“HKEY_LOCAL_MACHINE”。 **** 现在，可以看到名为  **BROKENSYSTEM** 的附加项。 为了进行故障排除，我们将这些有问题的配置单元装载为  **BROKENSYSTEM**。

8.  对 BROKENSYSTEM 分支进行以下更改：

    1.  检查 VM 是从哪个 **ControlSet** 注册表项启动的。 将在 HKLM\BROKENSYSTEM\Select\Current 中看到该项的数值。

    2.  若要启用规则，请打开以下注册表值：
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        然后，将 **Active=FALSE** 更改为 **Active=True**。
        
        **v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=@FirewallAPI.dll,-28775|Desc=@FirewallAPI.dll,-28756|EmbedCtxt=@FirewallAPI.dll,-28752|**

    3.  若要禁用规则，请打开以下注册表项：

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        然后，将 **Active=True** 更改为 **Active=FALSE**。
        
        **v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=@FirewallAPI.dll,-28775|Desc=@FirewallAPI.dll,-28756|EmbedCtxt=@FirewallAPI.dll,-28752|**

9.  突出显示“BROKENSYSTEM”，然后从菜单中选择“文件” > “卸载配置单元”。 **** **** 

10. [拆离系统磁盘并重新创建 VM](troubleshoot-recovery-disks-portal-windows.md)。

11. 检查是否解决了问题。