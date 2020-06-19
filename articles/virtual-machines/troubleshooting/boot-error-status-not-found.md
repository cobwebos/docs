---
title: 排查 Windows 启动管理器错误 - 0xC0000225“找不到状态”
description: 解决在 Azure VM 中出现错误代码 0xC0000225 的问题的步骤。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 817f9e362e639cbb8f0cc79607c376c0e8216ec7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663725"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>排查 Windows 启动管理器错误 - 0xC0000225“找不到状态”
 
本文提供了解决在 Azure VM 中出现错误代码 0xC0000225 的问题的步骤。 此错误表明找不到状态或对象。

## <a name="symptoms"></a>症状

使用[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)查看 VM 的屏幕截图时，将看到屏幕截图显示 Windows 无法启动错误，状态代码为 0xc0000225。

与此错误代码关联的文件将通知你采取哪些步骤来解决问题。 找到“文件:”部分的文本，以确定相应的操作方法。

### <a name="drivers-os-related-or-third-party"></a>驱动程序、OS 相关或第三方

如果存在文件，但引用了驱动程序（如图所示），或者与 OS 相关或是第三方文件，请按照[修复系统文件](#repair-the-system-file)部分下的步骤进行操作。
 
在下图中，Windows 启动管理器指出“Windows 未能启动。 原因可能是最近更改了硬件或软件。” 图中还显示“状态”为“0xc0000225”，“文件:”为 `\windows\System32\drivers\atapi.sys`，“信息:”为“无法加载操作系统，原因是关键系统驱动程序丢失或包含错误。”

![Windows 启动管理器指出“Windows 未能启动。 原因可能是最近更改了硬件或软件。” 它还显示“状态”为“0xc0000225”，“文件”为“\windows\System32\drivers\atapi.sys”，并在“信息”部分中指出：“无法加载操作系统，原因是关键系统驱动程序丢失或包含错误。”](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>无文件

如果显示状态代码，但未显示文件，请按照[添加 OSDEVICE 变量](#add-the-osdevice-variable)部分下的步骤进行操作。

在下图中，Windows 启动管理器指出“Windows 未能启动。 原因可能是最近更改了硬件或软件。” 图中还显示“状态”为“0xc0000225”，“信息:”为“启动选择失败，因为要求的设备不可访问。”

![Windows 启动管理器指出“Windows 未能启动。 原因可能是最近更改了硬件或软件。” 它还显示“状态”为“0xc0000225”，并在“信息”部分中指出：“启动选择失败，因为要求的设备不可访问。”](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>注册表文件

如果它引用任何注册表文件，例如 \windows\system32\config\system，请按照[创建支持票证](#contact-support)部分下的步骤进行操作。
 
在下图中，Windows 启动管理器指出“Windows 未能启动。 原因可能是最近更改了硬件或软件。” 图中还显示“状态”为“0xc0000225”，“文件”为 `\windows\System32\config\system`，“信息:”为“无法加载操作系统，原因是系统注册表文件丢失或包含错误。”

![Windows 启动管理器指出“Windows 未能启动。 原因可能是最近更改了硬件或软件。” 它还显示“状态”为“0xc0000225”，“文件”为“\windows\System32\config\system”，并在“信息”部分中指出：“无法加载操作系统，原因是系统注册表文件丢失或包含错误。”](./media/troubleshoot-boot-error-status-not-found/3.png)

在下图中，恢复屏幕显示“你的电脑/设备需要修复。 无法加载操作系统，原因是系统注册表文件丢失或包含错误。” 图中还显示错误代码为“0xc0000225”，“文件”为 `\windows\System32\config\system`。

![恢复屏幕显示“你的电脑/设备需要修复。 无法加载操作系统，原因是系统注册表文件丢失或包含错误。” 它还显示错误代码为“0xc0000225”，“文件”为“\windows\System32\config\system”。](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>原因

### <a name="missing-binary"></a>丢失二进制文件

在系统 (.sys) 文件中，可能会遇到二进制文件丢失或损坏的问题。

### <a name="bcd-corruption-or-improper-vhd-migration"></a>BCD 损坏或 VHD 迁移不当

在这种情况下，要么引导配置数据 (BCD) 已损坏，要么虚拟硬盘 (VHD) 已从本地迁移，但准备不充分。 结果导致 OSDEVICE 变量丢失，需要添加该变量。

### <a name="registry-hive-corruption"></a>注册表配置单元损坏

注册表配置单元损坏可能是由于：

- 配置单元失败
- 配置单元已装载，但为空
- 未正确关闭配置单元
## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述

1. 创建并访问修复 VM。
1. 选择解决方案：
   - [修复系统文件](#repair-the-system-file)
   - [添加 OSDevice 变量](#add-the-osdevice-variable)
   - [创建支持票证](#contact-support)
1. 启用串行控制台和内存转储收集。
1. 重新生成 VM。

### <a name="create-and-access-a-repair-vm"></a>创建并访问修复 VM

1. 使用 [VM 修复命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)的步骤 1-3 来准备一个修复 VM。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="select-a-solution"></a>选择解决方案

1. 打开权限提升的命令提示符。
1. 根据之前确定的症状，执行相应解决方案中的步骤。 你可以跳过其他解决方案中的步骤，因为它们不适用于你的问题：

- [修复系统文件](#repair-the-system-file)
- [添加 OSDevice 变量](#add-the-osdevice-variable)
- [创建支持票证](#contact-support)

### <a name="repair-the-system-file"></a>修复系统文件

1. 使用附加的 VHD，导航到虚拟机 (VM) 屏幕截图中显示的二进制文件的文件位置。
1. 右键单击该文件，选择“属性”，然后选择“详细信息”选项卡以查看文件信息。
   1. 请注意文件版本，如下图所示：

      ![“cng.sys”文件的属性窗口，突出显示了文件版本。](./media/troubleshoot-boot-error-status-not-found/5.png)

1. 将该文件重命名为 < BINARY.SYS >.old，并将 < BINARY.SYS > 替换为该文件的名称。

   对于上一步中的图像，文件 cng.sys 将重命名为 cng.sys.old

   > [!NOTE]
   > 如果尝试重命名该文件，但收到消息“文件已损坏且无法读取”，请[联系支持部门寻求帮助](https://azure.microsoft.com/support/create-ticket/)，因为此解决方案将不起作用。

1. 现在，损坏的文件已重命名，请通过从其内部存储库中还原来修复该文件。
   1. 启动 CMD 会话。
   1. 导航到 \windows\winsxs。

   1. 使用以下命令搜索位于此部分开头的二进制文件：

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      此命令将列出计算机拥有的该文件的所有版本，并提供该组件的路径历史记录。
      
      例如，dir cng.sys 将重命名为 dir cng.sys /s

   1. 在列表中选择该文件的最新版本（或你喜欢的任何版本），然后使用先前的路径和以下命令将该文件复制到 windows\system32 文件夹中：

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > 如果最新的二进制文件不起作用，请尝试使用该版本之前的版本，或者任何已知存在稳定文件的版本，例如应用补丁前的版本。

      例如，如果你要查找的二进制文件是 cmimcext.sys，故障驱动器为驱动器 F:，并且你刚刚运行了最新版本搜索，那么，你将看到下图，命令提示符 `dir cmim* /s` 中的查询找到了 cmimcext.sys 文件的最新版本。

      ![命令提示符“dir cmim* /s”中的查询，用于查找 cmimcext.sys 文件的最新版本。](./media/troubleshoot-boot-error-status-not-found/6.png)

      在上面的示例图中，查询是在 C: 上执行的，但驱动器号应为故障驱动器 F: 的驱动器号，该驱动器是作为修复 VM 上的数据磁盘附加的 OS 磁盘。

      最终用于复制文件的命令为：`copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers`。

完成此任务后，继续[启用串行控制台和内存转储收集](#enable-the-serial-console-and-memory-dump-collection)。

### <a name="add-the-osdevice-variable"></a>添加 OSDEVICE 变量

收集当前启动设置信息，并记下活动分区上的标识符。 然后，按照 VM 生成说明，使用此信息添加 OSDEVICE 变量。

如果此信息收集给出没有 \boot\bcd 文件的错误，则改用[修复系统文件](#repair-the-system-file)中的说明。 

1. 对于第 1 代 VM，请以管理员身份打开权限提升的命令提示符，然后输入以下命令：

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   此图显示了第 1 代 VM 中的 Windows 启动加载程序，并突出显示了标识符属性。 突出显示的标识符属性显示了唯一的字母数字字符串。

   ![Windows 启动加载程序显示在第 1 代 VM 中，并突出显示了标识符属性。 突出显示的标识符属性显示了唯一的字母数字字符串。](./media/troubleshoot-boot-error-status-not-found/7.png)

   记下 Windows 启动加载程序的标识符，其路径为 \windows\system32\winload.exe。

1. 对于第 2 代 VM，请验证 OS 磁盘是否联机，以及是否分配了分区驱动器号。 验证后，收集启动设置信息。
   1. 在“Windows 搜索”中，键入“磁盘管理”，然后打开磁盘管理控制台 。 使用此控制台可以识别修复 VM 上附加的磁盘号以及保存 BCD 存储的可扩展固件接口 (EFI) 分区。

   在下图中，磁盘 2 是附加到修复 VM 的磁盘号。 该图还显示了磁盘 2 上的 EFI 系统分区，该分区的大小为 100MB，并且未分配盘符。

   ![磁盘 2 显示为附加到修复 VM 的磁盘号。 它还显示了磁盘 2 上的 EFI 系统分区，该分区为 100MB，并且未分配盘符。](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. 以管理员身份打开权限提升的命令提示符，然后输入以下命令：
      1. 使用 `diskpart` 命令打开 DISKPART TOOL。
      1. 列出所有磁盘，然后选择上一步中标识的附加磁盘：
      
         ```
         list disk
         sel disk <DISK #>
         ```

         下图显示了列出和选择磁盘的结果。 列出了 Disk 0 (127 GB | Online)、Disk 1 (32 GB | Online) 和 Disk 2 (127 GB | Online)，并使用 `sel disk 2` 命令选择了 Disk 2。

         ![列出并选择磁盘的结果。 列出了 Disk 0 (127 GB | Online)、Disk 1 (32 GB | Online) 和 Disk 2 (127 GB | Online)，并选择了 Disk 2。](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. 列出分区，并选择上一步中标识的 EFI 系统分区：
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         下图显示了列出和选择分区的结果。 列出了 Partition 1 (Reserved | 16MB)、Partition 2 (System | 100MB) 和 Partition 3 (Primary | 126 GB)，并使用 `sel part 2` 命令选择了 Partition 2。

         ![列出并选择分区的结果。 列出了 Partition 1 (Reserved | 16MB)、Partition 2 (System | 100MB) 和 Partition 3 (Primary | 126 GB)，并选择了 Partition 2。](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. 使用 `assign` 命令向 EFI 分区分配一个盘符。

         在下图中，可以在文件资源管理器中看到 `assign` 命令和新驱动器 SYSTEM (F:)。

         ![可以在文件资源管理器中看到 assign 命令和新驱动器 SYSTEM (F:)。](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. 使用以下命令列出 BCD 存储数据：
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         在下图中，Windows 启动加载程序位于第 2 代 VM 中，并突出显示了标识符属性。 突出显示的标识符属性的值为 {default}。

         ![Windows 启动加载程序显示在第 2 代 VM 中，并突出显示了标识符属性。 突出显示的标识符属性将 default 显示为其值。](./media/troubleshoot-boot-error-status-not-found/12.png)

         记下 Windows 启动加载程序的标识符，其路径为 \windows\system32\winload.efi。

1. 请注意，活动分区上缺少 OSDEVICE 变量：

   ![命令提示符中列出了 Windows 启动管理器和 Windows 启动加载程序的属性，其中缺少 OSDEVICE 属性。](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   在此图中，命令提示符中列出了 Windows 启动管理器和 Windows 启动加载程序的属性，但缺少 OSDEVICE 属性。

1. 根据以下信息添加 OSDEVICE 变量：

   对于单分区 OS 磁盘，请添加 `BOOT`。

   > [!NOTE]
   > 可启动文件夹将与 Windows 文件夹 \windows folder 位于同一分区。
   > - 第 1 代 VM 的可引导文件夹为 (\boot\bcd folder)。
   > - 第 2 代 VM 的可引导文件夹为 EFI\Microsoft\boot\bcd。

   对于第 1 代 VM，请输入以下命令：

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   对于第 2 代 VM，请输入以下命令：

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   对于多分区 OS 磁盘，请添加 `PARTITION=<LETTER OF WINDOWS FOLDER>:`。

   > [!NOTE]
   > 可启动文件夹很可能与 Windows 文件夹 \windows folder 位于不同的分区。
   > - 第 1 代 VM 的可引导文件夹为 (\boot\bcd folder)。
   > - 第 2 代 VM 的可引导文件夹为 EFI\Microsoft\boot\bcd。

   对于第 1 代 VM，请输入以下命令：

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   对于第 2 代 VM，请输入以下命令：

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

完成此任务后，继续[启用串行控制台和内存转储收集](#enable-the-serial-console-and-memory-dump-collection)。

### <a name="contact-support"></a>联系支持人员

注册表文件错误已解决，但你需要[创建支持票证](https://azure.microsoft.com/support/create-ticket/)来获取更多帮助。

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储收集

**建议**：在重新生成 VM 之前，通过运行以下脚本来启用串行控制台和内存转储收集：

1. 以管理员身份打开权限提升的命令提示符会话。
1. 运行以下命令：

   **启用串行控制台**：
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. 验 OS 磁盘上的可用空间是否大于 VM 上的内存大小 (RAM)。

   如果 OS 磁盘上没有足够的空间，请更改将要创建内存转储文件的位置，并将该位置引用到具有足够可用空间的 VM 上附加的任何数据磁盘。 若要更改位置，请在以下命令中将 %SystemRoot% 替换为数据磁盘的驱动器号（例如，F:）。

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
