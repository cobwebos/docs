---
title: 排查通过 RDP 连接到 Azure 中的 Windows VM 时发生的常规错误 | Microsoft Docs
description: 了解如何排查通过 RDP 连接到 Azure 中的 Windows VM 时发生的常规错误 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: abff12e1a304c51cb0df394534c7da0a35518008
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089795"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>排查 Azure VM 的常规 RDP 错误

本文描述在与 Azure 中的 Windows 虚拟机 (VM) 建立远程桌面协议 (RDP) 连接时可能遇到的常规错误。

## <a name="symptom"></a>症状

与 Azure 中的 Windows VM 建立 RDP 连接时，可能会收到以下常规错误消息：

**由于以下原因之一，远程桌面无法连接到远程计算机：**

1. **未启用服务器的远程访问**

2. **远程计算机已关闭**

3. **远程计算机在网络中不可用**

**请确保远程计算机已打开并已连接到网络，并且已启用远程访问。**

## <a name="cause"></a>原因

此问题可能由以下原因导致：

### <a name="cause-1"></a>原因 1

RDP 组件已在如下所述的位置禁用：

- 组件级别
- 侦听器级别
- 终端服务器
- 远程桌面会话主机角色

### <a name="cause-2"></a>原因 2

远程桌面服务 (TermService) 未运行。

### <a name="cause-3"></a>原因 3

RDP 侦听器配置不当。

## <a name="solution"></a>解决方案

若要解决此问题，请[备份操作系统磁盘](../windows/snapshot-copy-managed-disk.md)，[将操作系统磁盘附加到救援 VM](troubleshoot-recovery-disks-portal-windows.md)，然后按步骤操作。

### <a name="serial-console"></a>串行控制台

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>步骤 1：在串行控制台中打开 CMD 实例

1. 选择“支持和故障排除” > “串行控制台(预览版)”访问 [串行控制台](serial-console-windows.md) 。 如果在 VM 上启用了该功能，则可以成功连接 VM。

2. 为 CMD 实例创建新通道。 键入 **CMD** 启动通道，以获取通道名称。

3. 切换到运行 CMD 实例的通道（在本例中应是通道 1）。

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>步骤 2：检查 RDP 注册表项的值:

1. 检查策略是否禁用 RDP。

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - 如果存在域策略，则会覆盖本地策略中的设置。
      - 如果域策略指出 RDP 已禁用 (1)，请从域控制器更新 AD 策略。
      - 如果域策略指出 RDP 已启用 (0)，则无需更新。
      - 如果域策略不存在，并且本地策略指出 RDP 已禁用 (1)，请使用以下命令启用 RDP： 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. 检查终端服务器的当前配置。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      如果该命令返回 0，则表示终端服务器已禁用。 在这种情况下，请按如下所示启用终端服务器：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. 如果服务器位于终端服务器场（RDS 或 Citrix）中，终端服务器模块将设置为排出模式。 检查终端服务器模块的当前模式。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      如果该命令返回 1，则表示终端服务器模块已设置为排出模式。 在这种情况下，请按如下所示将该模块设置为工作模式：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. 检查是否可以连接到终端服务器。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      如果该命令返回 1，则表示无法连接到终端服务器。 在这种情况下，请按如下所示启用连接：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. 检查 RDP 侦听器的当前配置。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      如果该命令返回 0，则表示 RDP 侦听器已禁用。 在这种情况下，请按如下所示启用侦听器：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. 检查是否可以连接到 RDP 侦听器。

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   如果该命令返回 1，则表示无法连接到 RDP 侦听器。 在这种情况下，请按如下所示启用连接：

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. 重启 VM。

8. 键入 `exit` 并按 **Enter** 两次，从 CMD 实例退出。

9. 通过键入 `restart` 重启 VM，然后连接到 VM。

如果仍发生此问题，请转到步骤 2。

#### <a name="step-2-enable-remote-desktop-services"></a>步骤 2：启用远程桌面服务

有关详细信息，请参阅[远程桌面服务在 Azure VM 上不启动](troubleshoot-remote-desktop-services-issues.md)。

#### <a name="step-3-reset-rdp-listener"></a>步骤 3：重置 RDP 侦听器

有关详细信息，请参阅[远程桌面经常在 Azure VM 中断开连接](troubleshoot-rdp-intermittent-connectivity.md)。

### <a name="offline-repair"></a>脱机修复

#### <a name="step-1-turn-on-remote-desktop"></a>步骤 1：启用远程桌面

1. [将 OS 磁盘附加到恢复 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 开始与恢复 VM 建立远程桌面连接。
3. 确保磁盘在磁盘管理控制台中标记为“联机”。 请注意分配给附加的 OS 磁盘的驱动器号。
4. 开始与恢复 VM 建立远程桌面连接。
5. 打开权限提升的命令提示符会话（“以管理员身份运行”）。 运行以下脚本。 对于此脚本，我们假设分配给附加 OS 磁盘的驱动器号为 F。请将此驱动器号替换为 VM 中的相应值。

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. 如果 VM 已加入域，请检查以下注册表项，以查看是否有某个组策略禁用了 RDP。 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      如果此注册表项的值设置为 1，则表示策略禁用了 RDP。 若要通过 GPO 策略启用远程桌面，请在域控制器中更改以下策略：

   
      **计算机配置\策略\管理模板：**

      策略定义\Windows 组件\远程桌面服务\远程桌面会话主机\连接\允许用户使用远程桌面服务进行远程连接
  
1. 从救援 VM 拆离磁盘。
1. [从磁盘创建新的 VM](../windows/create-vm-specialized.md)。

如果仍发生此问题，请转到步骤 2。

#### <a name="step-2-enable-remote-desktop-services"></a>步骤 2：启用远程桌面服务

有关详细信息，请参阅[远程桌面服务在 Azure VM 上不启动](troubleshoot-remote-desktop-services-issues.md)。

#### <a name="step-3-reset-rdp-listener"></a>步骤 3：重置 RDP 侦听器

有关详细信息，请参阅[远程桌面经常在 Azure VM 中断开连接](troubleshoot-rdp-intermittent-connectivity.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
