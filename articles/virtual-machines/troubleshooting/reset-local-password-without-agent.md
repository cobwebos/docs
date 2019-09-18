---
title: 在没有 Azure 代理的情况下重置本地 Windows 密码 | Microsoft Docs
description: 如何在 Azure 来宾代理未安装或者未在 VM 上正常运行的情况下重置本地 Windows 用户帐户密码
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 6faab5bffaddbbd5d8deb9c3834bf3d8fe3e3445
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058642"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>脱机重置 Azure VM 的本地 Windows 密码
如果已安装 Azure 来宾代理，可以使用 [Azure 门户或 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 重置 Azure 中 VM 的本地 Windows 密码。 此方法是重置 Azure VM 密码的主要方法。 如果遇到了 Azure 来宾代理无响应的问题，或者上传自定义映像后无法安装，可以手动重置 Windows 密码。 本文详细说明如何通过将源 OS 虚拟磁盘附加到另一个 VM 来重置本地帐户密码。 本文所述的步骤不适用于 Windows 域控制器。 

> [!WARNING]
> 只有在万不得已的情况下才使用此过程。 始终应该先尝试使用 [Azure 门户或 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 重置密码。

## <a name="overview-of-the-process"></a>过程概述
无法访问 Azure 来宾代理时，针对 Azure 中的 Windows VM 执行本地密码重置的核心步骤如下：

1. 停止受影响的 VM。
1. 为 VM 的 OS 磁盘创建快照。
1. 从快照创建 OS 磁盘的副本。
1. 将复制的 OS 磁盘附加并装入到另一个 Windows VM，并在磁盘上创建一些配置文件。 这些文件将帮助你重置密码。
1. 从故障排除 VM 中卸载并分离复制的 OS 磁盘。
1. 交换受影响的 VM 的 OS 磁盘。

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>资源管理器部署的 VM 的详细步骤

> [!NOTE]
> 这些步骤不适用于 Windows 域控制器。 仅适用于独立服务器或域成员服务器。

在执行以下步骤之前，始终应该尝试使用 [Azure 门户或 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 来重置密码。 在开始之前，请确保备份 VM。

1. 为受影响的 VM 的 OS 磁盘拍摄快照，从快照创建磁盘，然后将该磁盘附加到故障排除 VM。 有关详细信息，请参阅[使用 Azure 门户将 OS 磁盘附加到恢复 VM，对 WINDOWS VM 进行故障排除](troubleshoot-recovery-disks-portal-windows.md)。
2. 使用远程桌面连接到故障排除 VM。
3. 在源 VM 的驱动器上的 `\Windows\System32\GroupPolicy` 中创建 `gpt.ini`（如果存在 gpt.ini，请将它重命名为 gpt.ini.bak）：
   
   > [!WARNING]
   > 切勿在 C:\Windows（故障排除 VM 的 OS 驱动器）中意外创建以下文件。 应该在源 VM 的、作为数据磁盘附加的 OS 驱动器中创建以下文件。
   
   * 将以下代码行添加到创建的 `gpt.ini` 文件中：
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![创建 gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. 在 `\Windows\System32\GroupPolicy\Machines\Scripts\` 中创建 `scripts.ini` 确保隐藏的文件夹已显示。 如果需要，请创建 `Machine` 或 `Scripts` 文件夹。
   
   * 将以下代码行添加到创建的 `scripts.ini` 文件：
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![创建 scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. 在 `\Windows\System32` 中创建包含以下内容的 `FixAzureVM.cmd`，将 `<username>` 和 `<newpassword>` 替换为自己的值：
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![创建 FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    定义新密码时，必须符合针对 VM 配置的密码复杂性要求。

6. 在 Azure 门户中，将磁盘从故障排除 VM 中分离出来。

7. [更改受影响的 VM 的 OS 磁盘](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)。

8. 运行新 VM 后，使用在 `FixAzureVM.cmd` 脚本中指定的新密码通过远程桌面连接到该 VM。

9. 在与新 VM 建立的远程会话中，删除以下文件以清理环境：
    
    * 从 %windir%\System32 中
      * 删除 FixAzureVM.cmd
    * 从 %windir%\System32\GroupPolicy\Machine\Scripts 中
      * 删除 scripts.ini
    * 从 %windir%\System32\GroupPolicy 中
      * 删除 gpt.ini（如果 gpt.ini 事先已存在并已重命名为 gpt.ini.bak，请将 .bak 文件改回 gpt.ini）

## <a name="detailed-steps-for-classic-vm"></a>经典 VM 的详细步骤

> [!NOTE]
> 这些步骤不适用于 Windows 域控制器。 仅适用于独立服务器或域成员服务器。

在执行以下步骤之前，始终应该尝试使用 [Azure 门户或 Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) 来重置密码。 在开始之前，请确保备份 VM。 

1. 在 Azure 门户中删除受影响的 VM。 删除 VM 只会删除元数据，以及 Azure 中对 VM 的引用。 删除 VM 时，会保留虚拟磁盘：
   
   * 在 Azure 门户中选择 VM，然后单击 "*删除*"：
     
     ![删除现有 VM](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. 将源 VM 的 OS 磁盘附加到故障排除 VM。 故障排除 VM 必须与源 VM 的 OS 磁盘位于同一区域（例如 `West US`）：
   
   1. 在 Azure 门户中选择故障排除 VM。 单击“磁盘” | “附加现有磁盘”：
     
      ![附加现有磁盘](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. 选择“VHD 文件”，并选择包含源 VM 的存储帐户：
     
      ![选择存储帐户](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. 选中 "*显示经典存储帐户*" 框，然后选择源容器。 源容器通常为 *vhd*：
     
      ![选择存储容器](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![选择存储容器](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. 选择要附加的 OS VHD。 单击“选择”完成该过程：
     
      ![选择源虚拟磁盘](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. 单击 "确定" 以附加磁盘

      ![附加现有磁盘](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. 使用远程桌面连接到故障排除的 VM，确保源 VM 的 OS 磁盘可见：

   1. 在 Azure 门户中选择故障排除 VM，并单击“连接”。

   2. 打开下载的 RDP 文件。 输入故障排除 VM 的用户名和密码。

   3. 在文件资源管理器中，找到附加的数据磁盘。 如果源 VM 的 VHD 是附加到故障排除 VM 的唯一数据磁盘，它应该是 F: 驱动器：
     
      ![查看附加的数据磁盘](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. 在源 VM 的驱动器`gpt.ini` `gpt.ini` 上创建`gpt.ini.bak`（如果存在，请重命名为）： `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > 请确保不会意外地在中`C:\Windows`创建以下文件，即故障排除 VM 的 OS 驱动器。 应该在源 VM 的、作为数据磁盘附加的 OS 驱动器中创建以下文件。
   
   * 将以下代码行添加到创建的 `gpt.ini` 文件中：
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![创建 gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. 在 `\Windows\System32\GroupPolicy\Machines\Scripts\` 中创建 `scripts.ini` 确保隐藏的文件夹已显示。 如果需要，请创建 `Machine` 或 `Scripts` 文件夹。
   
   * 将以下代码行添加到创建的 `scripts.ini` 文件：

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![创建 scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. 在 `\Windows\System32` 中创建包含以下内容的 `FixAzureVM.cmd`，将 `<username>` 和 `<newpassword>` 替换为自己的值：
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![创建 FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    定义新密码时，必须符合针对 VM 配置的密码复杂性要求。

7. 在 Azure 门户中，从故障排除 VM 分离该磁盘：
   
   1. 在 Azure 门户中选择故障排除 VM，并单击“磁盘”。
   
   2. 选择步骤2中附加的数据磁盘，单击 "**分离**"，然后单击 **"确定"** 。

     ![分离磁盘](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![分离磁盘](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. 从源 VM 的 OS 磁盘创建一个 VM：
   
     ![从模板创建 VM](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![从模板创建 VM](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![从模板创建 VM](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>完成创建虚拟机体验

1. 运行新 VM 后，使用在 `FixAzureVM.cmd` 脚本中指定的新密码通过远程桌面连接到该 VM。

2. 在与新 VM 建立的远程会话中，删除以下文件以清理环境：
    
    * 从`%windir%\System32`
      * 取消`FixAzureVM.cmd`
    * 从`%windir%\System32\GroupPolicy\Machine\Scripts`
      * 取消`scripts.ini`
    * 从`%windir%\System32\GroupPolicy`
      * 删除`gpt.ini` （如果`gpt.ini`之前已存在，并且你已将`gpt.ini.bak`其重命名`.bak`为，请`gpt.ini`将该文件重命名回）

## <a name="next-steps"></a>后续步骤
如果仍然无法使用远程桌面建立连接，请参阅 [RDP 故障排除指南](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 [详细的 RDP 故障排除指南](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)探讨的是故障排除方法而不是具体的步骤。 也可以通过[提出 Azure 支持请求](https://azure.microsoft.com/support/options/)来获得人工协助。
