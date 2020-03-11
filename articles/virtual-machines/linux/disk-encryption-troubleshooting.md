---
title: Linux Vm 的 Azure 磁盘加密故障排除
description: 本文提供了有关 Microsoft Azure Linux Vm 的磁盘加密的故障排除提示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970483"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁盘加密故障排除指南

本指南面向使用 Azure 磁盘加密的组织中的 IT 专业人员、信息安全分析人员和云管理员。 本文旨在帮助排查与磁盘加密相关的问题。

执行以下任何步骤之前，请先确保你尝试加密的 Vm 在[支持的 vm 大小和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)中，并满足所有先决条件：

- [Vm 的其他要求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [网络要求](disk-encryption-overview.md#networking-requirements)
- [加密密钥存储要求](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS 磁盘加密故障排除

在通过全盘加密过程运行 Linux 操作系统 (OS) 磁盘加密之前，Linux 操作系统 (OS) 磁盘加密必须卸载 OS 驱动器。 如果无法卸载驱动器，则可能会出现错误消息“在发生以下情况后，卸载失败...”。

当尝试在具有已从受支持的股票库映像更改的环境的 VM 上尝试 OS 磁盘加密时，会发生此错误。 与支持的映像的偏差可能会影响扩展卸载 OS 驱动器的能力。 偏差的示例可包括以下项：
- 自定义映像不再与受支持文件系统或分区方案匹配。
- 加密之前在 OS 中安装并运行 SAP、MongoDB、Apache Cassandra 和 Docker 等大型应用程序时，将不支持这些应用程序。 “Azure 磁盘加密”在准备用于磁盘加密的 OS 驱动器时无法根据需要安全地关闭这些进程。 如果仍有活动的进程具备对 OS 驱动器打开的文件句柄，则 OS 驱动器无法卸载，这将导致 OS 驱动器加密失败。 
- 在启用加密的几乎同一时间内运行自定义脚本，或者在加密过程中在 VM 上进行其他任何更改。 如果 Azure 资源管理器模板定义了多个同时执行的扩展，或者在执行磁盘加密的同时运行自定义脚本扩展或其他操作，则可能会发生此冲突。 序列化并隔离此类步骤可能会解决问题。
- 在启用加密之前未禁用安全性增强的 Linux (SELinux)，因此卸载步骤将会失败。 完成加密后，可以重新启用 SELinux。
- OS 磁盘使用逻辑卷管理器 (LVM) 方案。 尽管可以使用有限的 LVM 数据磁盘支持，但无法使用 LVM OS 磁盘支持。
- 不满足最低内存要求（建议为 OS 磁盘加密提供 7 GB）。
- 数据驱动器以递归方式装载在 /mnt/ 目录下，或者相互装载（例如 /mnt/data1、/mnt/data2、/data3 + /data3/data4）。

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>更新 Ubuntu 14.04 LTS 的默认内核

Ubuntu 14.04 LTS 映像附带 4.4 版本的默认内核。 此内核版本存在一个已知问题，即 Out of Memory Killer 会在 OS 加密过程中不正确地终止 dd 命令。 此 bug 已在最新 Azure 优化 Linux 内核中修复。 若要避免此错误，在映像中启用加密之前，使用以下命令更新至 [Azure 优化内核 4.15](https://packages.ubuntu.com/trusty/linux-azure) 或更高版本：

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

VM 重启进入新内核后，可以使用以下方式确认新内核版本：

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>更新 Azure 虚拟机代理和扩展版本

使用不受支持的 Azure 虚拟机代理版本的虚拟机映像上的 azure 磁盘加密操作可能会失败。 在启用加密之前，应更新具有早于2.2.38 的代理版本的 Linux 映像。 有关详细信息，请参阅[如何更新 VM 上的 Azure Linux 代理](../extensions/update-linux-agent.md)和[azure 中虚拟机代理的最低版本支持](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

此外，还需要正确的 AzureDiskEncryption 或 AzureDiskEncryptionForLinux 来宾代理扩展版本的版本。）。 满足 Azure 虚拟机代理先决条件并使用虚拟机代理的受支持版本时，平台将自动维护和更新扩展版本。

Microsoft.ostcextensions. AzureDiskEncryptionForLinux 扩展已弃用，不再受支持。  

## <a name="unable-to-encrypt-linux-disks"></a>无法加密 Linux 磁盘

在某些情况下，Linux 磁盘加密看上去停滞在“OS 磁盘加密已启动”状态，同时 SSH 处于禁用状态。 加密过程可能需要 3-16 小时才能完成存储库映像。 如果添加了多 TB 大小的数据磁盘，此过程可能需要数天才能完成。

Linux OS 磁盘加密序列暂时卸载 OS 驱动器。 然后，它将对整个 OS 磁盘进行逐块加密，然后再将其重新安装为加密状态。 进行加密时，Linux 磁盘加密不允许同时使用 VM。 VM 的性能特点会在完成加密所需的时间上产生显著差异。 这些特点包括磁盘大小以及存储帐户为标准还是高级 (SSD) 存储。

若要检查加密状态，请轮询从[AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus)命令返回的**ProgressMessage**字段。 加密 OS 驱动器时，VM 会进入维护状态，同时会禁用 SSH，以防止对进行中的进程造成任何干扰。 进行加密时，EncryptionInProgress 消息大部分时间都在提供报告。 几个小时之后，VMRestartPending 消息会提示重新启动 VM。 例如：


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

系统会提示重启 VM，在 VM 重启后，必须等待 2-3 分钟，以重启并在目标上执行最终步骤。 当加密最终完成时，状态消息会发生更改。 显示此消息后，加密的 OS 驱动器预期可供使用，并且 VM 可恢复使用。

在以下情况下，建议将 VM还原到就在加密前一刻获取的快照或备份：
   - 如果上述的重新启动序列不会发生。
   - 如果启动信息、进度消息或其他错误指示器报告 OS 加密过程已失败。 本指南中以“卸载失败”错误为例，介绍了一个消息。

在下次尝试之前，请重新评估 VM 的特征，并确保满足所有先决条件。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>防火墙保护下的 Azure 磁盘加密故障排除

参阅[隔离网络上的磁盘加密](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>加密状态故障排除 

即使在 VM 内未加密后，门户也可能会将磁盘显示为已加密。  当使用低级别命令从 VM 内部直接解密磁盘，而不是使用较高级别的 Azure 磁盘加密管理命令时，会发生这种情况。  较高级别的命令不仅从 VM 内解密磁盘，还会更新与 VM 关联的重要平台级别的加密设置和扩展设置。  如果这些不是按对齐方式进行的，则平台将无法报告加密状态或正确预配 VM。   

若要使用 PowerShell 禁用 Azure 磁盘加密，请使用[AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)后跟[AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)。 禁用加密之前，运行 AzVMDiskEncryptionExtension 会失败。

若要使用 CLI 禁用 Azure 磁盘加密，请使用[az vm Encryption disable](/cli/azure/vm/encryption)。 

## <a name="next-steps"></a>后续步骤

本文档已详细描述有关 Azure 磁盘加密的一些常见问题和解决这些问题的方法。 有关此服务及其功能的详细信息，请参阅以下文章：

- [在 Azure 安全中心应用磁盘加密](../../security-center/security-center-apply-disk-encryption.md)
- [Azure 静态数据加密](../../security/fundamentals/encryption-atrest.md)
