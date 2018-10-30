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
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988952"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>远程桌面服务在 Azure VM 上不启动

本文介绍如何排查连接到 Azure 虚拟机 (VM) 时远程桌面服务 (TermService) 不启动或无法启动的问题。

>[!NOTE]
>Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍资源管理器部署模型的用法。 建议为新部署使用此模型，而不是使用经典部署模型。

## <a name="symptoms"></a>症状

尝试连接到 VM 时遇到以下情况：

- VM 屏幕截图显示操作系统已满载，并正在等待凭据。
- VM 上的所有应用程序按预期方式工作并且可以访问它们。
- VM 正在响应 Microsoft 远程桌面协议 (RDP) 端口（默认为 3389）上的 TCP 连接。
- 尝试建立 RDP 连接时，系统不提示输入凭据。

## <a name="cause"></a>原因

之所以发生此问题，是因为远程桌面服务未在虚拟机上运行。 未运行的原因取决于以下情况：

- TermService 服务设置为“已禁用”。
- TermService 服务崩溃或挂起。

## <a name="solution"></a>解决方案

若要解决此问题，使用以下解决方法之一，或逐个尝试不同的解决方法：

### <a name="solution-1-using-the-serial-console"></a>解决方法 1：使用串行控制台

1. 选择“支持和故障排除” > “串行控制台(预览版)”访问 [串行控制台](serial-console-windows.md) 。 如果在 VM 上启用了该功能，则可以成功连接 VM。

2. 为 CMD 实例创建新通道。 键入 **CMD** 启动通道，以获取通道名称。

3. 切换到运行 CMD 实例的通道（在本例中应是通道 1）。

   ```
   ch -si 1
   ```

4. 再次按 **Enter** 并键入 VM 的有效用户名和密码（本地或域 ID）。

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

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>解决方法 2：使用恢复 VM 来启用该服务

[备份 OS 磁盘](../windows/snapshot-copy-managed-disk.md)并[将 OS 磁盘附加到救援 VM](../windows/troubleshoot-recovery-disks-portal.md)。 然后，打开权限提升的 CMD 实例，并在救援 VM 上运行以下脚本：

>[!NOTE]
>假设分配给附加 OS 磁盘的驱动器号为 F。请将其替换为 VM 中的相应值。 此操作完成后，从恢复 VM 中分离该磁盘，然后[重新创建 VM](../windows/create-vm-specialized.md)。 若要进一步进行故障排除，可以使用**解决方法 1**，因为串行控制台已启用。

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
