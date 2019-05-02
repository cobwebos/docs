---
title: 故障排除 - 为 IaaS VM 启用 Azure 磁盘加密 | Microsoft Docs
description: 本文提供适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密的故障排除提示。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 4715ec92c4ee45733cc0eb2839c533f9ee8968fe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694117"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁盘加密故障排除指南

本指南面向使用 Azure 磁盘加密的组织中的 IT 专业人员、信息安全分析人员和云管理员。 本文旨在帮助排查与磁盘加密相关的问题。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS 磁盘加密故障排除

在通过全盘加密过程运行 Linux 操作系统 (OS) 磁盘加密之前，Linux 操作系统 (OS) 磁盘加密必须卸载 OS 驱动器。 如果无法卸载驱动器，则可能会出现错误消息“在发生以下情况后，卸载失败...”。

在通过受支持的储备库映像更改的目标 VM 环境上尝试加密 OS 磁盘时，会发生此错误。 与支持的映像的偏差可能会影响扩展卸载 OS 驱动器的能力。 偏差的示例可包括以下项：
- 自定义映像不再与受支持文件系统或分区方案匹配。
- 加密之前在 OS 中安装并运行 SAP、MongoDB、Apache Cassandra 和 Docker 等大型应用程序时，将不支持这些应用程序。 “Azure 磁盘加密”在准备用于磁盘加密的 OS 驱动器时无法根据需要安全地关闭这些进程。 如果仍有活动的进程具备对 OS 驱动器打开的文件句柄，则 OS 驱动器无法卸载，这将导致 OS 驱动器加密失败。 
- 在启用加密的几乎同一时间内运行自定义脚本，或者在加密过程中在 VM 上进行其他任何更改。 如果 Azure 资源管理器模板定义了多个同时执行的扩展，或者在执行磁盘加密的同时运行自定义脚本扩展或其他操作，则可能会发生此冲突。 序列化并隔离此类步骤可能会解决问题。
- 在启用加密之前未禁用安全性增强的 Linux (SELinux)，因此卸载步骤将会失败。 完成加密后，可以重新启用 SELinux。
- OS 磁盘使用逻辑卷管理器 (LVM) 方案。 尽管可以使用有限的 LVM 数据磁盘支持，但无法使用 LVM OS 磁盘支持。
- 不满足最低内存要求（建议为 OS 磁盘加密提供 7 GB）。
- 数据驱动器以递归方式装载在 /mnt/ 目录下，或者相互装载（例如 /mnt/data1、/mnt/data2、/data3 + /data3/data4）。
- 不满足 Linux 的其他 Azure 磁盘加密[先决条件](azure-security-disk-encryption-prerequisites.md)。

## <a name="bkmk_Ubuntu14"></a> 更新 Ubuntu 14.04 LTS 默认内核

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

Azure 磁盘加密操作使用不受支持的版本的 Azure 虚拟机代理的虚拟机映像上可能会失败。 在启用加密之前，应更新使用早于 2.2.38 代理版本的 Linux 映像。 有关详细信息，请参阅[如何更新 VM 上的 Azure Linux 代理](../virtual-machines/extensions/update-linux-agent.md)并[在 Azure 中的虚拟机代理的最低版本支持](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

此外，还需要 Microsoft.Azure.Security.AzureDiskEncryption 或 Microsoft.Azure.Security.AzureDiskEncryptionForLinux 来宾代理扩展的正确版本。 维护并满足 Azure 虚拟机代理先决条件并且使用支持的虚拟机代理版本时，自动更新由平台扩展版本。

Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux 扩展已弃用，不再受支持。  

## <a name="unable-to-encrypt-linux-disks"></a>无法加密 Linux 磁盘

在某些情况下，Linux 磁盘加密看上去停滞在“OS 磁盘加密已启动”状态，同时 SSH 处于禁用状态。 加密过程可能需要 3-16 小时才能完成存储库映像。 如果添加了多 TB 大小的数据磁盘，此过程可能需要数天才能完成。

Linux OS 磁盘加密序列暂时卸载 OS 驱动器。 然后，它将对整个 OS 磁盘进行逐块加密，然后再将其重新安装为加密状态。 与 Windows 上的 Azure 磁盘加密不同，Linux 磁盘加密不允许在加密的同时并发使用 VM。 VM 的性能特点会在完成加密所需的时间上产生显著差异。 这些特点包括磁盘大小以及存储帐户为标准还是高级 (SSD) 存储。

若要检查的加密状态，轮询**ProgressMessage**从返回的字段[Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus)命令。 加密 OS 驱动器时，VM 会进入维护状态，同时会禁用 SSH，以防止对进行中的进程造成任何干扰。 进行加密时，EncryptionInProgress 消息大部分时间都在提供报告。 几个小时之后，VMRestartPending 消息会提示重新启动 VM。 例如：


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
如果连接受到防火墙、代理要求或网络安全组 (NSG) 设置的限制，扩展执行所需任务的能力可能会受到干扰。 此干扰可能会导致出现类似于“VM 上未提供扩展状态”的状态消息。 在预期方案中，将无法完成加密。 以下部分描述了可能需要调查的一些常见防火墙问题。

### <a name="network-security-groups"></a>网络安全组
应用的任何网络安全组设置仍必须允许终结点满足所述的与磁盘加密相关的网络配置[先决条件](azure-security-disk-encryption-prerequisites.md#bkmk_GPO)。

### <a name="azure-key-vault-behind-a-firewall"></a>防火墙保护下的 Azure Key Vault

使用 [Azure AD 凭据](azure-security-disk-encryption-prerequisites-aad.md)启用加密时，目标 VM 必须允许连接到 Azure Active Directory 终结点和密钥保管库终结点。 当前 Azure Active Directory 身份验证终结点在 [Office 365 URL 和 IP 地址范围](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges)文档中的第 56 和 59 节中进行维护。 在有关如何[访问防火墙保护下的 Azure 密钥保管库](../key-vault/key-vault-access-behind-firewall.md)的文档中提供了密钥保管库说明。

### <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务 
VM 必须能够访问这样的 [Azure 实例元数据服务](../virtual-machines/windows/instance-metadata-service.md)终结点：该终结点使用只能从 VM 内访问的已知不可路由 IP 地址 (`169.254.169.254`)。  不支持 alter 本地 HTTP 流量传输到此地址 （例如，添加 X 转发的标头） 的代理配置。

### <a name="linux-package-management-behind-a-firewall"></a>防火墙保护下的 Linux 程序包管理

在运行时，启用加密之前，适用于 Linux 的 Azure 磁盘加密依赖于目标分发版的程序包管理系统来安装所需的必备组件。 如果防火墙设置阻止 VM 下载并安装这些组件，则后续的失败就在意料之中。 配置此程序包管理系统的步骤会因分发版而异。 在 Red Hat 上，如果需要代理，则必须确保正确设置订阅管理器和 yum。 有关详细信息，请参阅[如何排除有关订阅管理器和 yum 的问题](https://access.redhat.com/solutions/189533)。  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core 疑难解答

在 Windows Server 2016 Server Core 上，bdehdcfg 组件默认不可用。 Azure 磁盘加密需要此组件。 它用于从 OS 卷拆分出系统卷，该操作仅在虚拟机生命期内执行一次。 在后续加密操作中，不需要这些二进制文件。

要暂时避开此问题，请将下面 Windows Server 2016 Data Center VM 中的 4 个文件复制到 Server Core 上的同一位置中：

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. 输入以下命令：

   ```
   bdehdcfg.exe -target default
   ```

1. 此命令将创建一个 550 MB 系统分区。 重新启动系统。

1. 使用 DiskPart 检查卷，然后继续。  

例如：

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>加密状态故障排除 

在门户可能显示为加密磁盘，即使已在 VM 中未加密。  低级别的命令用于直接解密 VM，而不是使用更高级别 Azure 磁盘加密管理命令中的将磁盘从可以发生该错误。  更高级别命令不仅解密从 VM 中的磁盘，VM 外部它们还必须更新重要平台级别的加密设置和与 VM 关联的扩展插件设置。  如果这些不保存在对齐方式，请在平台不能以报告加密状态或正确预配 VM。   

若要正确地禁用 Azure 磁盘加密，从已知的良好状态启动已启用，加密，然后使用[禁用 AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)并[删除 AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) Powershell命令，或[az vm 加密禁用](/cli/azure/vm/encryption)CLI 命令。 

## <a name="next-steps"></a>后续步骤

本文档已详细描述有关 Azure 磁盘加密的一些常见问题和解决这些问题的方法。 有关此服务及其功能的详细信息，请参阅以下文章：

- [在 Azure 安全中心应用磁盘加密](../security-center/security-center-apply-disk-encryption.md)
- [Azure 静态数据加密](azure-security-encryption-atrest.md)
