---
title: 在 Azure 中远程连接到 Windows 10 或 Windows Server 2016 VM 时，对 netvsc.sys 问题进行故障排除 | Microsoft Docs
description: 了解在 Azure 中连接到 Windows 10 或 Windows Server 2016 VM 时如何对与 netsvc.sys 相关的 RDP 问题进行故障排除。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: e6685a5e77d92bb9e05ab9578e48c99e80a64b74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362248"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>由于 netvsc.sys，无法在 Azure 中远程连接到 Windows 10 或 Windows Server 2016 VM

本文介绍如何解决在连接到 Hyper-V Server 2016 主机上的 Windows 10 或 Windows Server 2016 Datacenter 虚拟机 (VM) 时没有网络连接的问题。

## <a name="symptoms"></a>症状

无法使用远程桌面协议 (RDP) 连接到 Azure Windows 10 或 Windows Server 2016 VM。 在[启动诊断](boot-diagnostics.md)中，屏幕在网络接口卡 (NIC) 处显示一个红叉。 这表示操作系统完全加载后 VM 没有连接。

通常，此问题发生在 Windows [内部版本 14393](https://support.microsoft.com/help/4093120/) 和[内部版本 15063](https://support.microsoft.com/help/4015583/) 中。 如果操作系统版本低于这些版本，则本文不适用于你的方案。 要检查系统版本，请在[串行访问控制台功能](serial-console-windows.md)中打开 CMD 会话，然后运行“Ver”。

## <a name="cause"></a>原因

如果已安装的 netvsc.sys 系统文件的版本为 10.0.14393.594 或 10.0.15063.0，则可能会发生此问题。 这些版本的 netvsc.sys 可能会阻止系统与 Azure 平台进行交互。


## <a name="solution"></a>解决方案

在执行这些步骤之前，请[创建受影响 VM 的系统磁盘快照](../windows/snapshot-copy-managed-disk.md)作为备份。 若要排查此问题，请使用串行控制台，或通过将 VM 的系统磁盘附加到恢复 VM 来[脱机修复 VM](#repair-the-vm-offline)。


### <a name="use-the-serial-console"></a>使用串行控制台

连接到[串行控制台并打开 PowerShell 实例](serial-console-windows.md)，然后按照这些步骤进行操作。

> [!NOTE]
> 如果 VM 上未启用串行控制台，请转到[修复 VM 脱机](#repair-the-vm-offline)部分。

1. 在 PowerShell 实例中运行以下命令以获取文件的版本 (c:\windows\system32\drivers\netvsc.sys)：

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. 将适当的更新下载到从同一区域连接到正常工作的 VM 的新数据磁盘或现有数据磁盘：

   - **10.0.14393.594**：[KB4073562](https://support.microsoft.com/help/4073562)  或更高版本的更新
   - **10.0.15063.0**：[KB4016240](https://support.microsoft.com/help/4016240) 或更高版本的更新

3. 从正常工作的 VM 中分离实用程序磁盘，然后将其附加到中断的 VM。

4. 运行以下命令以在 VM 上安装更新：

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. 重启 VM。

### <a name="repair-the-vm-offline"></a>修复 VM 脱机

1. [将系统磁盘附加到恢复 VM](../windows/troubleshoot-recovery-disks-portal.md)。

2. 开始与恢复 VM 建立远程桌面连接。

3. 确保磁盘在磁盘管理控制台中标记为“联机”。 请留意分配给附加系统磁盘的驱动器号。

4. 创建 \Windows\System32\config 文件夹的副本，以防需要回滚更改。

5. 在安全的 VM 中，启动注册表编辑器 (regedit.exe)。

6. 选择 HKEY_LOCAL_MACHINE 键，然后选择菜单中的“文件” > “加载配置单元”。

7. 查找 \Windows\System32\config 文件夹中的系统文件。

8. 选择“打开”，键入 BROKENSYSTEM 作为名称，展开 HKEY_LOCAL_MACHINE 键，然后查找名为 BROKENSYSTEM 的附加键。

9. 请转到以下位置：

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. 在每个子项（例如 0000）中，检查显示为“Microsoft HYPER-V 网络适配器”的 DriverDesc 值。

11. 在子项中，检查 DriverVersion 值，该值是 VM 的网络适配器的驱动程序版本。

12. 下载相应的更新：

    - **10.0.14393.594**：[KB4073562](https://support.microsoft.com/help/4073562)  或更高版本的更新
    - **10.0.15063.0**：[KB4016240](https://support.microsoft.com/help/4016240) 或更高版本的更新

13. 将系统磁盘作为数据磁盘附加到可以下载更新的安全 VM 上。

14. 运行以下命令以在 VM 上安装更新：

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. 运行以下命令来卸载配置单元：

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [拆离系统磁盘并重新创建 VM](../windows/troubleshoot-recovery-disks-portal.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
