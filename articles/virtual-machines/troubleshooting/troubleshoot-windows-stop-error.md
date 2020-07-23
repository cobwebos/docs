---
title: Windows 停止错误 - 状态无内存
description: 本文提供了一些步骤，用于解决 Windows 无法启动和显示状态 "无内存" 的问题。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 67064cf694445acf8472b958660133c2f2d31db9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85660783"
---
# <a name="windows-stop-error---status-no-memory"></a>Windows 停止错误 - 状态无内存

本文提供了一些步骤，用于解决 Windows 无法启动和显示状态或错误代码 #0xC0000017 （也称为 "无内存"）的问题。

## <a name="symptom"></a>症状

当你使用[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)来查看虚拟机（VM）的屏幕截图时，你会看到屏幕截图显示了错误代码： `0xC0000017` 。 根据你所运行的 Windows 的版本，你可能会看到此代码显示在**Windows 启动管理器**或**恢复屏幕**中。

   **Windows 启动管理器**

   ![Windows 启动管理器指出 "Windows 无法启动。 最近的硬件或软件更改可能导致 ""。 向下滚动，将看到状态代码 "0xC0000017" 以及说明 "无法加载应用程序或操作系统，因为所需的文件丢失或包含错误" 的信息。](./media/troubleshoot-windows-stop-error/1.png)

   **恢复屏幕**
 
   !["恢复" 屏幕上指出 "您的 PC/设备需要修复。 没有足够的内存可用于创建 ramdisk 设备 "。 还应看到错误代码 "0xC0000017"。](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>原因

操作系统的磁盘已满、太碎，或者操作系统（OS）无法访问内存或页面文件，或者两者都无法访问。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述：

1. 创建并访问修复 VM
1. 释放磁盘上的空间
1. 清除 BCD 存储中的错误内存
1. 将页面文件还原到其默认位置
1. 启用串行控制台和内存转储收集
1. 重新生成 VM

> [!NOTE]
> 遇到此错误时，来宾操作系统无法正常运行。 需要在脱机模式下进行故障排除来解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 来准备一个修复 VM。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>对于第2代 Vm，请为可扩展固件接口（EFI）分区分配一个字母：

如果使用第2代 VM，则附加磁盘的 EFI 分区可能没有分配给它。 在继续执行此故障排除指南之前，你需要执行以下步骤以将一个字母分配给分区。

1. 在 Windows search 中，输入 `diskmgmt` 并打开 "**磁盘管理" 控制台**。
1. 确定附加到修复 VM 的损坏磁盘。 通常，此磁盘列在控制台的最后，并且具有最大的数值。
1. 请注意，如果在该磁盘中有一个分区保存**EFI 系统分区**，该分区也没有分配给它的字母值（例如驱动器*F：*）。 如果分配了所有分区，则可以跳到释放磁盘上的空间。 否则，请继续向此磁盘分配一个字母。

   ![磁盘管理控制台（附加磁盘为 "Disk 2"）以及未分配分区为 100 MB，为 "EFI 系统分区"。](./media/troubleshoot-windows-stop-error/3.png)

1. 以管理员身份打开提升的命令提示符，然后输入 `diskpart` 启动**DISKPART**工具。
1. 输入以下命令：

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - 在命令中，将替换 `<NUMBER OF THE ATTACHED DISK>` 为你在步骤2中标识的磁盘编号。
   - 在命令中，将替换 `<NUMBER OF THE SYSTEM PARTION>` 为 EFI 系统分区的分区号。 尚未为此分区分配驱动器号，但它应为类型**系统**并且大小约为 100 mb。

   > [!NOTE]
   > 将磁盘管理控制台中的分区与 DISKPART 工具中列出的分区进行比较有助于确定哪个分区号对应于附加磁盘中的 EFI 系统分区。

1. 关闭命令提示符窗口。

### <a name="free-up-space-on-the-disk"></a>释放磁盘上的空间

由于损坏的磁盘已附加到修复 VM，因此应该验证该磁盘上的操作系统是否有足够的可用空间来正常运行。 

1. 右键单击附加磁盘的驱动器，然后选择 "**属性**"，检查磁盘是否已满。
1. 如果磁盘的**可用空间小于 300 Mb**，请[使用 PowerShell 将其扩展到最大 1 Tb](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk)。
1. 磁盘大小为**1 Tb**后，将需要执行磁盘清理。 可以使用 "[磁盘清理" 工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)释放空间。
1. 打开提升的命令提示符（以管理员身份运行）实例，并在驱动器上执行碎片整理：

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - 根据碎片级别，碎片整理可能需要数小时。
   - 在命令中，将替换 `<LETTER ASSIGNED TO THE OS DISK>` 为 OS 磁盘的驱动器号（例如驱动器*F：*）。

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>清除引导配置数据（BCD）存储中的错误内存

1. 打开提升的命令提示符（以管理员身份运行）。
1. 用以下命令在启动配置文件中查询错误内存标志：

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - 在命令中，将替换 `<LETTER ASSIGNED TO THE OS DISK>` 为 OS 磁盘的驱动器号（例如驱动器*F：*）。

1. 如果查询未显示错误的内存块，请跳到下一任务。 否则，请继续执行步骤4。
1. 如果识别到错误的内存块，这些块会阻止创建 ramdisk，并应使用以下命令将其删除：

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - 在命令中，将替换 `<LETTER ASSIGNED TO THE OS DISK>` 为 OS 磁盘的驱动器号（例如驱动器*F：*）。

### <a name="restore-the-page-file-to-its-default-location"></a>将页面文件还原到其默认位置

页面文件将计算机的随机存取内存（RAM）无法容纳的数据存储为溢出/备份的形式。 文件可能托管在 VHD 中，而不是 temp 驱动器，这是默认的 Azure 位置。 如果为 true，则文件可能不可访问，应还原到默认位置。

在执行任何步骤之前，应在正常的磁盘上创建**\windows\system32\config**文件夹的副本。 此步骤可确保可以撤消任何不需要的更改。 你将处理重要的系统文件，因此强烈建议使用这种预防措施。

1. 在 Windows search 中，输入**regedit** ，并打开注册表编辑器应用程序。
1. 在注册表编辑器中，突出显示键**HKEY_LOCAL_MACHINE**然后从菜单中选择 "**文件 >" "加载配置单元 ...** "。

   ![注册表编辑器的 "加载配置单元" 菜单。](./media/troubleshoot-windows-stop-error/4.png)

1. 在 "加载配置单元" 对话框中，选择 " **\windows\system32\config\SYSTEM** "，并单击 "打开"。
   1. 系统将提示你输入名称，你应输入**BROKENSYSTEM**。 在进行故障排除时，此名称将有助于区分受影响的配置单元。
   1. 展开 " **HKEY_LOCAL_MACHINE** "，查看已添加的新 BROKENSYSTEM 密钥。
1. 使用注册表编辑器确定计算机启动的 ControlSet。
   1. 导航到**HKEY_LOCAL_MACHINE >> BROKENSYSTEM " >> 选择**"。
   1. 在列出的密钥中，记下当前的数据值。 例如，如果此值为**1**或**0x00000001 （1）**，则控件集将为 ControlSet001。
1. 检查页面文件创建的配置位置。
   1. 在 HKEY_LOCAL_MACHINE \BROKENSYSTEM 中，展开与在步骤4中标识的 ControlSet 号匹配的目录，例如**ControlSet001**。
   1. 导航到**控件 >> 会话管理器 >> 内存管理**，并记下**ExistingPageFiles**密钥的位置。
   1. 此密钥应位于 Temp 驱动器的默认 Azure 位置。 如果该路径不存在，并且位于另一个位置（例如数据磁盘驱动器或 OS 驱动器）中的 VHD 中，则需要将其删除。
   1. 在文件资源管理器中浏览到该位置，然后删除**pagefile.sys**文件。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储收集

**建议**：在重新生成 VM 之前，通过运行以下脚本来启用串行控制台和内存转储收集：

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 以管理员身份打开权限提升的命令提示符会话。
1. 列出 BCD 存储数据，并确定要在下一步中使用的启动加载程序标识符。

   1. 对于第1代 VM，请输入以下命令，并记下列出的标识符：
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - 在命令中， `<BOOT PARTITON>` 将替换为附加磁盘中包含启动文件夹的分区号。

      ![在第1代虚拟机中列出 BCD 存储的输出，该 VM 在 Windows 启动加载程序中列出标识符号。](./media/troubleshoot-windows-stop-error/5.png)

   1. 对于第2代 VM，请输入以下命令，并记下列出的标识符：
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - 在命令中，将替换 `<LETTER OF THE EFI SYSTEM PARTITION>` 为 EFI 系统分区的盘符。
   - 启动 "磁盘管理" 控制台来确定标记为**EFI 系统分区**的适当系统分区可能会很有帮助。
   - 标识符可以是唯一的 GUID，也可以是默认的**bootmgr**。

1. 运行以下命令以启用串行控制台：

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - 在命令中，将替换 `<VOLUME LETTER WHERE THE BCD FOLDER IS>` 为 BCD 文件夹的字母。
   - 在命令中，将替换 `<BOOT LOADER IDENTIFIER>` 为在上一步中找到的标识符。

1. 验 OS 磁盘上的可用空间是否大于 VM 上的内存大小 (RAM)。

   如果 OS 磁盘上没有足够的空间，请更改将要创建内存转储文件的位置，并将该位置引用到具有足够可用空间的 VM 上附加的任何数据磁盘。 要更改位置，请在以下命令中将 **% SystemRoot%** 替换为数据磁盘的驱动器号，如驱动器**F：**。

   用于启用 OS 转储的建议配置：

   **从损坏的 OS 磁盘加载注册表配置单元：**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **在 ControlSet001 上启用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **在 ControlSet002 上启用：**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **卸载损坏的 OS 磁盘：**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>重新生成 VM

使用 [VM 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 重新生成 VM。
