---
title: 在脱机模式下安装 Azure VM 代理 | Microsoft Docs
description: 了解如何在脱机模式下安装 Azure VM 代理。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 8b9d1ea3e4b5bf959484a737453d7f6f1f63d1e6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>在脱机模式下安装 Azure 虚拟机代理 

Azure 虚拟机代理（VM 代理）可提供多种有用的功能，例如本地管理员密码重置和脚本推送。 本文演示如何为脱机的 Windows 虚拟机 (VM) 安装 VM 代理。 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>何时在脱机模式下使用 VM 代理

如有以下情况，可在脱机模式下安装 VM 代理：

- 已部署的 Azure VM 未安装 VM 代理，或代理不能正常工作。
- 忘记了 VM 的管理员密码，或无法访问 VM。

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>如何在脱机模式下安装 VM 代理

可以使用以下步骤，在脱机模式下安装 VM 代理。

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>第 1 步：将 VM 的 OS 磁盘作为数据磁盘附加到另一 VM

1.  删除 VM。 删除 VM 时，请确保选择“保留磁盘”选项。

2.  将该 OS 磁盘作为数据磁盘附加到另一 VM（即故障排除 VM）。 有关详细信息，请参阅[在 Azure 门户中将数据磁盘附加到 Windows VM](attach-managed-disk-portal.md)。

3.  连接到故障排除 VM。 转到“计算机管理” > “磁盘管理”。 确认 OS 磁盘处于联机状态，并且已将驱动器号分配到磁盘分区。

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>第 2 步：修改 OS 磁盘以安装 Azure VM 代理

1.  远程桌面连接到故障排除 VM。

2.  在附加的 OS 磁盘上，浏览到 \windows\system32\config 文件夹。 将此文件夹中的所有文件复制为备份，以备回滚之需。

3.  启动注册表编辑器 (regedit.exe)。

4.  选择“HKEY_LOCAL_MACHINE”项。 在菜单上，选择“文件” > “加载配置单元”：

    ![加载配置单元](./media/install-vm-agent-offline/load-hive.png)

5.  浏览到已附加 OS 磁盘上的 \windows\system32\config\SYSTEM 文件夹。 输入“BROKENSYSTEM”作为配置单元名称。 新的注册表配置单元将显示在“HKEY_LOCAL_MACHINE”项之下。

6.  浏览到已附加 OS 磁盘上的 \windows\system32\config\SOFTWARE 文件夹。 输入“BROKENSOFTWARE”作为配置单元软件。

7.  如果 VM 代理不能正常工作，请备份当前配置。

    >[!NOTE]
    >如果 VM 未安装代理，请继续执行第 8 步。 
      
    1. 将 \windowsazure 文件夹重命名为 \windowsazure.old。

    2. 导出以下注册表：
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  将故障排除 VM 上的现有文件用作 VM 代理安装的存储库。 完成以下步骤：

    1. 从故障排除 VM 中，以注册表格式 (.reg) 导出以下子项： 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![导出注册表子项](./media/install-vm-agent-offline/backup-reg.png)

    2. 编辑注册表文件。 在每个文件中，将项值 SYSTEM改为 BROKENSYSTEM（如下图所示）并保存该文件。

        ![更改注册表子项值](./media/install-vm-agent-offline/change-reg.png)

    3. 双击每个注册表文件，将注册表文件导入存储库。

    4. 确认将以下三个子项成功导入 BROKENSYSTEM 配置单元：
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  将 C:\windowsazure\packages 中的 VM 代理文件夹复制到&lt;已附加 OS 磁盘&gt;:\windowsazure\packages。

    ![将 VM 代理文件复制到 OS 磁盘](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >请勿复制 logs 文件夹。 在服务启动后，将生成新的日志。

10.  选择“BROKENSYSTEM”。 在菜单上，选择“文件” > “卸载配置单元”

11.  选择“BROKENSOFTWARE”。 在菜单上，选择“文件” > “卸载配置单元”

12.  分离 OS 磁盘，然后使用该 OS 磁盘重新创建 VM。

13.  访问 VM。 请注意，RdAgent 正在运行，并且正在生成日志。

如果使用经典部署模型创建了 VM，则无需再进行额外操作。


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>对使用 Azure 资源管理器创建的 VM 使用 ProvisionGuestAgent 属性

如果使用资源管理器部署模型创建了 VM，请使用 Azure PowerShell 模块更新 ProvisionGuestAgent 属性。 该属性会通知 Azure 该 VM 已安装 VM 代理。

若要设置 ProvisionGuestAgent 属性，请在 Azure PowerShell 中运行以下命令：

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

然后运行 `Get-AzureVM` 命令。 请注意，GuestAgentStatus 属性现已得到数据填充：

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>后续步骤

- [Azure 虚拟机代理概述](agent-user-guide.md)
- [适用于 Windows 的虚拟机扩展和功能](extensions-features.md)
