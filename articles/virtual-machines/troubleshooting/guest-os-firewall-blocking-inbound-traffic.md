---
title: Azure VM 来宾 OS 防火墙阻止入站流量 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 6e90b164fac4ea1123f5f9a43eea1169d93d9a04
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154026"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM 来宾 OS 防火墙阻止入站流量

本文介绍如何修复来宾操作系统防火墙阻止入站流量时出现的远程桌面门户 (RDP) 问题。

## <a name="symptoms"></a>症状

无法使用 RDP 连接来连接 Azure 虚拟机 (VM)。 从“启动诊断”->“屏幕截图”中，可看到操作系统已在欢迎屏幕 (Ctrl+Alt+Del) 上完全加载。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

未设置 RDP 规则来允许 RDP 流量。

### <a name="cause-2"></a>原因 2

来宾系统防火墙配置文件设置为阻止所有入站连接，包括 RDP 流量。

![防火墙设置](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>解决方案

在执行这些步骤之前，请创建受影响 VM 的系统磁盘快照作为备份。 有关详细信息，请参阅 [拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

要解决此问题，请使用[如何使用远程工具解决 Azure VM 问题](remote-tools-troubleshoot-azure-vm-issues.md)中介绍的方法远程连接到 VM，然后将来宾操作系统防火墙规则编辑为“允许”RDP 流量。

### <a name="online-troubleshooting"></a>联机故障排除

连接到[串行控制台并打开 PowerShell 实例](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。 如果 VM 上未启用串行控制台，请转到“[脱机修复 VM](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)”。

#### <a name="mitigation-1"></a>缓解措施 1

1.  如果已在 VM 上安装并正常运行 Azure 代理，则可以使用 VM 菜单上“支持 + 故障排除” > “重置密码”下的“仅重置配置”选项。

2.  运行此恢复选项将执行以下操作：

    *   启用 RDP 组件（如果处于禁用状态）。

    *   启用所有 Windows 防火墙配置文件。

    *   确保在 Windows 防火墙中启用 RDP 规则。

    *   如果前面的步骤不起作用，请手动重置防火墙规则。 为此，请运行以下命令查询所有名称中含有“远程桌面”的规则：

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        如果 RDP 端口设置为 3389 以外的任何其他端口，则必须查找可能已创建并设置为此端口的任何自定义规则。 要查询使用自定义端口的所有入站规则，请运行以下命令：

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  若发现该规则已禁用，请将其启用。 要打开整个组（例如内置的远程桌面组），请运行以下命令：

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    或者，要打开特定的远程桌面 (TCP-In) 规则，请运行以下命令：

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  要进行故障排除，可将防火墙配置文件设置为 OFF：

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    完成故障排除并正确设置防火墙后，请重新启用防火墙。

    > [!Note]
    > 无需重启 VM 即可应用这些更改。

5.  尝试建立 RDP 连接以访问 VM。

#### <a name="mitigation-2"></a>缓解措施 2

1.  查询防火墙配置文件，确定入站防火墙策略是否设置为“ *BlockInboundAlways*”：

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > 以下指南适用于防火墙策略，具体取决于其设置方式：
    >    * *BlockInbound*：除非存在有效规则允许该流量，否则将阻止所有入站流量。
    >    * *BlockInboundAlways*：忽略所有防火墙规则，并阻止所有流量。

2.  编辑“ *DefaultInboundAction*” ，将这些配置文件设置为“ **允许**”流量 。 为此，请运行以下命令：

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  再次查询配置文件，确保已成功完成更改。 为此，请运行以下命令：

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > 无需重启 VM 即可应用更改。

4.  再次尝试通过 RDP 访问 VM。

### <a name="offline-mitigations"></a>脱机缓解措施

1.  [将系统磁盘附加到恢复 VM](troubleshoot-recovery-disks-portal-windows.md)。

2.  开始与恢复 VM 建立远程桌面连接。

3.  确保在磁盘管理控制台中将该磁盘标记为“ **联机**” 。 请留意分配给附加系统磁盘的驱动器号。

#### <a name="mitigation-1"></a>缓解措施 1

请参阅 [如何在来宾 OS 上启用/禁用某个防火墙规则](enable-disable-firewall-rule-guest-os.md)。

#### <a name="mitigation-2"></a>缓解措施 2

1.  [将系统磁盘附加到恢复 VM](troubleshoot-recovery-disks-portal-windows.md)。

2.  开始与恢复 VM 建立远程桌面连接。

3.  将系统磁盘附加到恢复 VM 后，请确保在磁盘管理控制台中将该磁盘标记为“ **联机**” 。 请注意分配给附加的 OS 磁盘的驱动器号。

4.  打开提升后的 CMD 实例，然后运行以下脚本：

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [拆离系统磁盘并重新创建 VM](troubleshoot-recovery-disks-portal-windows.md)。

6.  检查是否解决了问题。
