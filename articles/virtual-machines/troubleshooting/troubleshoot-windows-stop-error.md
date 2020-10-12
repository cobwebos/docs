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
ms.openlocfilehash: 33b4c59e14301e496d0eddafa7bdfdf201b7aa29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005899"
---
# <a name="windows-stop-error---status-no-memory"></a>Windows 停止错误 - 状态无内存

本文提供了一些步骤，用于解决 Windows 无法启动和显示状态或错误代码 #0xC0000017 （也称为 "无内存"）的问题。

## <a name="symptom"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看虚拟机 (VM) 的屏幕截图时，你将看到屏幕截图显示错误代码：`0xC0000017`。 根据你正在运行的 Windows 版本，你可能会看到此代码显示在在“Windows 引导管理器”或“恢复”屏幕中 。

   Windows 引导管理器

   ![Windows 启动管理器指出 "Windows 无法启动。 最近的硬件或软件更改可能导致 ""。 向下滚动，将看到状态代码 "0xC0000017" 以及说明 "无法加载应用程序或操作系统，因为所需的文件丢失或包含错误" 的信息。](./media/troubleshoot-windows-stop-error/1.png)

   “恢复”屏幕
 
   !["恢复" 屏幕上指出 "您的 PC/设备需要修复。 没有足够的内存可用于创建 ramdisk 设备 "。 还应看到错误代码 "0xC0000017"。](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>原因

操作系统的磁盘已满、太碎或操作系统 (操作系统) 无法访问内存或页面文件，或者两者都无法访问。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述：

1. 创建并访问修复 VM
1. 释放磁盘上的空间
1. 清除 BCD 存储中的错误内存
1. 将页面文件还原到其默认位置
1. 启用串行控制台和内存转储收集
1. 重建 VM

> [!NOTE]
> 遇到此错误时，来宾操作系统无法正常运行。 需要在脱机模式下进行故障排除来解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 来准备一个修复 VM。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>对于第 2 代 VM，向可扩展固件接口 (EFI) 分区分配一个驱动器号：

如果你使用第 2 代 VM，则附加磁盘的 EFI 分区可能未分配有驱动器号。 在继续执行此故障排除指南之前，你需要执行以下步骤，向分区分配一个驱动器号。

1. 在 Windows Search 中，输入 `diskmgmt`，然后打开“磁盘管理”控制台。
1. 确定附加到修复 VM 的受损磁盘。 通常，此磁盘列于控制台的最后，并且具有最大的数值。
1. 请注意，如果在该磁盘中有一个保存 **EFI 系统分区**的分区，也没有为其分配字母值 (例如驱动器 *F：*) 。 如果所有分区都已分配驱动器号，你可跳过以释放磁盘上的空间。 否则，请继续向此磁盘分配一个驱动器号。

   ![磁盘管理控制台（附加磁盘为 "Disk 2"）以及未分配分区为 100 MB，为 "EFI 系统分区"。](./media/troubleshoot-windows-stop-error/3.png)

1. 以管理员身份打开提升的命令提示符，并输入 `diskpart` 以启动 DISKPART 工具。
1. 输入以下命令：

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - 在该命令中，将 `<NUMBER OF THE ATTACHED DISK>` 替换为在步骤 2 中标识的磁盘号。
   - 在该命令中，将 `<NUMBER OF THE SYSTEM PARTION>` 替换为 EFI 系统分区的分区号。 此分区尚未分配有驱动器号，但它应该是一个系统分区类型并且大小约为 100 MB。

   > [!NOTE]
   > 将“磁盘管理”控制台中的分区与 DISKPART 工具中列出的分区进行比较，这有助于确定哪个分区号对应于附加磁盘中的 EFI 系统分区。

1. 关闭命令提示符窗口。

### <a name="free-up-space-on-the-disk"></a>释放磁盘上的空间

受损磁盘现已附加到修复 VM，你应该验证该磁盘上的 OS 是否有足够的可用空间以正常运行。 

1. 右键单击附加磁盘的该驱动器并选择“属性”，检查磁盘是否已满。
1. 如果磁盘的可用空间少于 300 MB，请[使用 PowerShell 将其最大扩展到 1 TB](../windows/expand-os-disk.md)。
1. 一旦磁盘大小达到 1 TB，你需要执行磁盘清理。 可使用[磁盘清理工具](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)来释放空间。
1. 打开提升的命令提示符（以管理员身份运行）实例，并在驱动器上执行碎片整理：

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - 根据碎片级别，碎片整理可能需要几个小时。
   - 在命令中，将替换为 `<LETTER ASSIGNED TO THE OS DISK>` OS 磁盘的驱动器号 (例如驱动器 *F：*) 。

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>清除引导配置数据 (BCD) 存储中的错误内存

1. 打开提升的命令提示符（以管理员身份运行）。
1. 用以下命令在引导配置文件中查询错误内存标志：

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - 在命令中，将替换为 `<LETTER ASSIGNED TO THE OS DISK>` OS 磁盘的驱动器号 (例如驱动器 *F：*) 。

1. 如果查询未显示错误的内存块，请跳至下一任务。 否则，请继续执行步骤 4。
1. 如果系统识别到错误的内存块，你应使用以下命令将其删除，这些块会阻止创建 ramdisk：

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - 在命令中，将替换为 `<LETTER ASSIGNED TO THE OS DISK>` OS 磁盘的驱动器号 (例如驱动器 *F：*) 。

### <a name="restore-the-page-file-to-its-default-location"></a>将页面文件还原到其默认位置

页面文件存储了计算机随机访问内存中无法保存的数据 (RAM) 作为溢出/备份的形式。 文件可能托管在 VHD 中，而不是临时驱动器中（这是默认的 Azure 位置）。 在这种情况下，文件可能不可访问，且应被还原到默认位置。

在执行任何步骤之前，你应在正常的磁盘上创建一个 \windows\system32\config 文件夹副本。 此步骤可确保任何不需要的更改均能够撤消。 你将处理重要的系统文件，因此我们强烈建议你采取这种预防措施。

1. 在 Windows Search 中，输入 regedit 并打开“注册表编辑器”应用程序。
1. 在“注册表编辑器”中，突出显示 HKEY_LOCAL_MACHINE 项，然后从菜单中选择“文件\加载配置单元…” 。

   ![注册表编辑器中的“加载配置单元”菜单。](./media/troubleshoot-windows-stop-error/4.png)

1. 在“加载配置单元”对话框中，选择 \windows\system32\config\SYSTEM 并单击“打开”。
   1. 系统将提示你输入名称，你应输入 BROKENSYSTEM。 在你执行故障排除时，此名称有助于区分受影响的配置单元。
   1. 展开 HKEY_LOCAL_MACHINE，查看添加的新项 BROKENSYSTEM。
1. 使用“注册表编辑器”确定计算机从哪个控件集启动。
   1. 导航到“HKEY_LOCAL_MACHINE”>>“BROKENSYSTEM”>>“选择”。
   1. 在列出的项中，记下 Current 的数据值。 例如，如果此值为 1 或 0x00000001 (1)，则该控件集将为 ControlSet001 。
1. 检查为创建页面文件所配置的位置。
   1. 在 HKEY_LOCAL_MACHINE\BROKENSYSTEM 中，展开与在步骤 4 中标识的控件集编号（例如 ControlSet001）匹配的目录，。
   1. 导航到“控件”>>“会话管理器”>>“内存管理”，并记下 ExistingPageFiles 项的位置 。
   1. 此项应位于临时驱动器这一默认的 Azure 位置。 如果该路径不存在，并且位于另一个位置（例如数据磁盘驱动器或 OS 驱动器）中的 VHD 中，则需要将其删除。
   1. 在文件资源管理器中浏览到该位置，然后删除 pagefile.sys 文件。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储收集

**建议**：在重新生成 VM 之前，通过运行以下脚本来启用串行控制台和内存转储收集：

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 以管理员身份打开权限提升的命令提示符会话。
1. 列出 BCD 存储数据，并确定要在下一步中使用的引导加载程序标识符。

   1. 对于第 1 代 VM，请输入以下命令，并记下列出的标识符：
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - 在该命令中，将 `<BOOT PARTITON>` 替换为附加磁盘中包含引导文件夹的分区驱动器号。

      ![该输出列出了第 1 代 VM 中的 BCD 存储，标识符编号在 Windows 引导加载程序下方列出。](./media/troubleshoot-windows-stop-error/5.png)

   1. 对于第 2 代 VM，请输入以下命令，并记下列出的标识符：
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - 在该命令中，将 `<LETTER OF THE EFI SYSTEM PARTITION>` 替换为 EFI 系统分区的驱动器号。
   - 这可能有助于启动“磁盘管理”控制台以识别标记为 EFI 系统分区的相应系统分区。
   - 标识符可以是唯一的 GUID，也可以是默认的 bootmgr。

1. 运行以下命令以启用串行控制台：

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - 在该命令中，将 `<VOLUME LETTER WHERE THE BCD FOLDER IS>` 替换为 BCD 文件夹的驱动器号。
   - 在该命令中，将 `<BOOT LOADER IDENTIFIER>` 替换为在上一步骤中找到的标识符。

1. 验 OS 磁盘上的可用空间是否大于 VM 上的内存大小 (RAM)。

   如果 OS 磁盘上没有足够的空间，请更改将要创建内存转储文件的位置，并将该位置引用到具有足够可用空间的 VM 上附加的任何数据磁盘。 若要更改位置，请在以下命令中将 %SystemRoot% 替换为数据磁盘的驱动器号（例如，驱动器 F:）。

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

使用 [VM 修复命令的步骤 5](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新生成 VM。
