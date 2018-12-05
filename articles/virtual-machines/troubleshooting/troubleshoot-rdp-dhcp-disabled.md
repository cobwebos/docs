---
title: 因为 DHCP 被禁用而无法远程连接到 Azure 虚拟机 | Microsoft Docs
description: 了解如何解决由于 DHCP 客户端服务在 Microsoft Azure 中被禁用而导致的 RDP 问题 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: a469fe0d6057d865ec006d9eb14ad95f2d4b7005
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308425"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>由于 DHCP 客户端服务被禁用，因此无法对 Azure 虚拟机进行 RDP

本文介绍了在 Azure Windows 虚拟机 (VM) 中禁用 DHCP 客户端服务后无法通过远程桌面连接到该 VM 的问题。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>症状 

无法在 Azure 中与 VM 建立 RDP 连接，因为 DHCP 客户端服务在 VM 中被禁用。 在 Azure 门户中的[“启动诊断”](../troubleshooting/boot-diagnostics.md)中检查屏幕截图时，你看到 VM 正常启动并且在登录屏幕中等待凭据。 在使用事件查看器远程查看 VM 中的事件日志时， 发现 DHCP 客户端服务未启动或无法启动。 下面是示例日志：

**日志名称**: 系统 </br>
**源**: 服务控制管理器 </br>
**日期**: 2015 年 12 月 16 日上午 11:19:36 </br>
**事件 ID**: 7022 </br>
**任务类别**: 无 </br>
**级别**: 错误 </br>
**关键字**: 经典</br> 
**用户**: 不适用 </br>
**计算机**: myvm.cosotos.com</br>
**说明**：DHCP 客户端服务在启动时挂起。</br>

对于资源管理器 VM，可使用串行访问控制台功能，通过以下命令查询事件日志 7022：

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

对于经典 VM，需要在“脱机”模式下工作，并手动收集日志。

## <a name="cause"></a>原因

DHCP 客户端服务未在 VM 上运行。 

> [!NOTE]
> 本文仅适用于 DHCP 客户端服务，而不适用于 DHCP 服务器。 

## <a name="solution"></a>解决方案 

在执行这些步骤之前，请创建受影响 VM 的 OS 磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

若要解决此问题，请使用串行控制台启用 DHCP 或者为 VM [重置网络接口](reset-network-interface.md)。

### <a name="use-serial-control"></a>使用串行控制台

1. 连接到[串行控制台并打开 CMD 实例](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
)。 如果未在 VM 上启用串行控制台，请参阅[重置网络接口](reset-network-interface.md)。
2. 在网络接口上检查是否禁用了 DHCP：

        sc query DHCP
3. 如果 DHCP 停止，请尝试启动该服务

        sc start DHCP
        
4. 再次查询服务，确保它已成功启动。

        sc query DHCP

    尝试连接到 VM 并查看问题是否得以解决。
5. 如果服务未启动，请根据所接收到的错误消息，使用以下适当的解决方案：

    | 错误  |  解决方案 |
    |---|---|
    | 5- 访问被拒绝  | 请参阅 [DHCP 客户端服务由于访问被拒绝错误而停止](#dhcp-client-service-is-stopped-because-of-an-access-denied-error)。  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | 请参阅 [DHCP 客户端服务崩溃或挂起](#dhcp-client-service-crashes-or-hangs)。  |
    | 1058 - ERROR_SERVICE_DISABLED  | 请参阅 [DHCP 客户端服务被禁用](#dhcp-client-service-is-disabled)。  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。   |
    | 1067 - ERROR_PROCESS_ABORTED |请参阅 [DHCP 客户端服务崩溃或挂起](#dhcp-client-service-crashes-or-hangs)。   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | 请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  请参阅 [DHCP 客户端服务由于登录失败而失败](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | 请参阅 [DHCP 客户端服务崩溃或挂起](#dhcp-client-service-crashes-or-hangs)。  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | 请参阅 [DHCP 客户端服务被禁用](#dhcp-client-service-is-disabled)。  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | 请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。  | 
    |1053 | 请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。  |
    

#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP 客户端服务由于访问被拒绝错误而停止

1. 连接到[串行控制台](serial-console-windows.md#)并打开 PowerShell 实例。
2. 运行以下脚本下载进程监视器工具：

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```
3. 现在启动 **procmon** 跟踪：

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```
4. 通过启动生成“拒绝被访问”消息的服务来再现问题： 

   ```
   sc start DHCP
   ```

   如果启动失败，请终止进程监视器跟踪：

   ```   
   procmon /Terminate 
   ```
5. 收集 c:\temp\ProcMonTrace.PML 文件：

    1. [将数据磁盘附加到 VM](../windows/attach-managed-disk-portal.md
)。
    2. 使用串行控制台可将文件复制到新驱动器。 例如，`copy C:\temp\ProcMonTrace.PML F:\`。 在此命令中，F 是附加的数据磁盘的驱动程序号。 使用正确的值适当地替换该字母。
    3. 分离数据驱动器，然后将其附加到已安装进程监视器 ubstakke 的正常 VM。

6. 在正常的 VM 上使用进程监视器打开 ProcMonTrace.PML。 然后按“结果为‘访问被拒绝’”进行筛选，如以下屏幕截图所示： ****

    ![在进程监视器中按结果进行筛选](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. 修复输出中的注册表项、文件夹或文件。 通常，出现此问题的原因是服务中使用的登录帐户没有 ACL 权限，因此无法访问这些对象。 若要确定登录帐户的正确 ACL 权限，可以在正常的 VM 上检查。 

#### <a name="dhcp-client-service-is-disabled"></a>DHCP 客户端服务被禁用

1. 将服务还原为其默认启动值：

   ```
   sc config DHCP start= auto
   ```

2. 启动服务：

   ```
   sc start DHCP
   ```

3. 再次查询服务状态，确保它正在运行：

   ```
   sc query DHCP
   ```

4. 尝试使用远程桌面连接到 VM。

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP 客户端服务由于登录失败而失败

1. 由于此服务的启动帐户一经更改便会出现此问题，因此请将帐户还原为其默认状态： 

        sc config DHCP obj= 'NT Authority\Localservice'
2. 启动服务：

        sc start DHCP
3. 尝试使用远程桌面连接到 VM。

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP 客户端服务崩溃或挂起
1. 如果服务状态卡在“正在启动”或“正在停止”状态，请尝试停止服务： 

        sc stop DHCP
2. 在服务自身的“svchost”容器中隔离该服务：

        sc config DHCP type= own
3. 启动服务：

        sc start DHCP
4. 如果服务仍未启动，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="repair-the-vm-offline"></a>修复 VM 脱机

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>将 OS 磁盘附加到恢复 VM

1. [将 OS 磁盘附加到恢复 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 开始与恢复 VM 建立远程桌面连接。 确保附加的磁盘在磁盘管理控制台中标记为“联机”。 请注意分配给附加的 OS 磁盘的驱动器号。
3.  打开权限提升的命令提示符实例（“以管理员身份运行”）。 然后运行以下脚本。 此脚本假设分配给附加的 OS 磁盘的驱动器号为 F。使用 VM 中的值适当地替换该字母。 

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service 
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [分离 OS 磁盘并重新创建 VM](../windows/troubleshoot-recovery-disks-portal.md)。 然后检查是否解决了问题。

## <a name="next-steps"></a>后续步骤

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。


