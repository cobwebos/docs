---
title: 远程桌面服务在 Azure VM 上不启动 | Microsoft Docs
description: 了解如何排查使用远程桌面服务连接到虚拟机时出现的问题 | Microsoft Docs
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238856"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>远程桌面服务在 Azure VM 上不启动

本文介绍如何排查连接到 Azure 虚拟机 (VM) 时远程桌面服务 (TermService) 不启动或无法启动的问题。

>[!NOTE]
>Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍资源管理器部署模型的用法。 建议为新部署使用此模型，而不是使用经典部署模型。

## <a name="symptoms"></a>症状

尝试连接到 VM 时遇到以下情况：

- VM 屏幕截图显示操作系统已满载，并正在等待凭据。

    ![VM 状态的屏幕截图](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- 使用事件查看器远程查看 VM 中的事件日志，可以看到远程桌面服务 (TermServ) 未启动或无法启动的消息。 下面是示例日志：

    **日志名称**: 系统 </br>
    **源**: 服务控制管理器 </br>
    **日期**: 12/16/2017 11:19:36 AM</br>
    **事件 ID**: 7022</br>
    **任务类别**: 无</br>
    **级别**: 错误</br>
    **关键字**: 经典</br>
    **用户**: 不适用</br>
    **计算机**: vm.contoso.com</br>
    **说明**: 远程桌面服务服务在启动时挂起。 

    也可以使用串行访问控制台功能通过以下查询查找这些错误： 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>原因
 
之所以发生此问题，是因为远程桌面服务未在 VM 上运行。 未运行的原因取决于以下情况： 

- TermService 服务设置为“已禁用”。 
- TermService 服务崩溃或挂起。 

## <a name="solution"></a>解决方案

若要排查此问题，请使用串行控制台，或通过将 VM 的 OS 磁盘附加到恢复 VM 来[修复 VM 脱机](#repair-the-vm-offline)。

### <a name="use-serial-console"></a>使用串行控制台

1. 选择“支持和故障排除” > “串行控制台”来访问 [串行控制台](serial-console-windows.md)。 如果在 VM 上启用了该功能，则可以成功连接 VM。

2. 为 CMD 实例创建新通道。 键入 **CMD** 启动通道，以获取通道名称。

3. 切换到正在运行 CMD 实例的通道。 在本例中，它应该是通道 1。

   ```
   ch -si 1
   ```

4. 再次按 **Enter** 并输入 VM 的有效用户名和密码（本地或域 ID）。

5. 查询 TermService 服务的状态。

   ```
   sc query TermService
   ```

6. 如果服务状态显示为“已停止”，请尝试启动该服务。

    ```
    sc start TermService
     ``` 

7. 再次查询服务，确保它已成功启动。

   ```
   sc query TermService
   ```
    如果该服务无法启动，请根据收到的错误遵循相应的解决方法：

    |  错误 |  建议 |
    |---|---|
    |5- 访问被拒绝 |请参阅 [TermService 服务由于访问被拒绝错误而停止](#termService-service-is-stopped-because-of-access-denied-error) |
    |1058 - ERROR_SERVICE_DISABLED  |请参阅 [TermService 服务已禁用](#termService-service-is-disabled)。  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题|
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题    |
    |1070 - ERROR_SERVICE_START_HANG   | 请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题  |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | 请参阅 [TermService 服务已禁用](#termService-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题 |
    |1753   |请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>TermService 服务由于访问被拒绝错误而停止

1. 连接到[串行控制台](serial-console-windows.md#)并打开 PowerShell 实例。
2. 运行以下脚本下载进程监视器工具：

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. 现在启动 procmon 跟踪：

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. 通过启动出现访问被拒绝错误的服务来再现问题： 

        sc start TermService 
        
    如果启动失败，请继续终止进程监视器跟踪：

        procmon /Terminate 
5. 收集文件  **c:\temp\ProcMonTrace.PML**，使用 procmon 将其打开，然后按“结果为‘访问被拒绝’”进行筛选，如以下屏幕截图所示： ****

    ![在进程监视器中按结果进行筛选](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. 修复输出中的注册表项、文件夹或文件。 通常，出现此问题的原因是服务中使用的登录帐户没有 ACL 权限，因此无法访问这些对象。 若要了解登录帐户的正确 ACL 权限，可以在正常的 VM 上检查。 

#### <a name="termservice-service-is-disabled"></a>TermService 服务已禁用

1.  将服务还原为其默认启动值：

        sc config TermService start= demand 
        
2.  启动服务：

        sc start TermService 
3.  再次查询服务的状态，确保服务正在运行：sc query TermService 
4.  尝试使用远程桌面连接到 VM。


### <a name="repair-the-vm-offline"></a>修复 VM 脱机

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>将 OS 磁盘附加到恢复 VM

1. [将 OS 磁盘附加到恢复 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 开始与恢复 VM 建立远程桌面连接。 确保附加的磁盘在磁盘管理控制台中标记为“联机”。 请注意分配给附加的 OS 磁盘的驱动器号。
3.  打开权限提升的命令提示符实例（“以管理员身份运行”），然后运行以下脚本。 假设分配给附加 OS 磁盘的驱动器号为 F。请将其替换为 VM 中的相应值。 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [拆离 OS 磁盘并重新创建 VM](../windows/troubleshoot-recovery-disks-portal.md)，然后检查问题是否得以解决。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
