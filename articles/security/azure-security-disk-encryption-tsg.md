---
title: "Azure 磁盘加密故障排除 | Microsoft 文档"
description: "本文提供适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密的故障排除提示。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: f2b9aad02a1ae3d5117ffd59b448eabe65a936fc
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁盘加密故障排除指南

本指南面向 IT 专业人员、信息安全分析人员，以及组织中使用 Azure 磁盘加密并需要获得有关排查磁盘加密相关问题的指导的云管理员。

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS 磁盘加密故障排除

在通过全盘加密过程运行 Linux 操作系统 (OS) 磁盘加密之前，Linux 操作系统 (OS) 磁盘加密必须卸载 OS 驱动器。 如果无法卸载驱动器，则可能会出现错误消息 “无法卸载…”。

在通过受支持的储备库映像修改或更改的目标 VM 环境上尝试加密 OS 磁盘更有可能发生此错误。 与支持的映像存在偏差，从而可能会妨碍扩展卸载 OS 驱动器的示例如下所示：
- 自定义映像不再与受支持文件系统或分区方案匹配。
- 加密之前在 OS 中安装并运行了 SAP、MongoDB 或 Apache Cassandra 等大型应用程序。 该扩展不能正确关闭这些应用程序。 如果这些应用程序将打开的文件句柄保留到 OS 驱动器，则驱动器无法卸载，从而导致失败。
- 在启用加密的几乎同一时间内运行自定义脚本，或者在加密过程中在 VM 上进行其他任何更改。 如果 Azure 资源管理器模板定义了多个同时执行的扩展，或者在执行磁盘加密的同时运行自定义脚本扩展或其他操作，则可能会发生此冲突。 序列化并隔离此类步骤可能会解决问题。
- 在启用加密之前未禁用安全性增强的 Linux (SELinux)，卸载步骤将会失败。 完成加密后，可以重新启用 SELinux。
- OS 磁盘使用逻辑卷管理器 (LVM) 方案。 尽管此时可以使用有限的 LVM 数据磁盘支持，但无法使用 LVM OS 磁盘支持。
- 不满足最低内存要求（建议为 OS 磁盘加密提供 7GB）。
- 数据驱动器以递归方式装载在 /mnt/ 目录下，或者相互装载（例如 /mnt/data1、/mnt/data2、/data3 + /data3/data4）。
- 不满足 Linux 的其他 Azure 磁盘加密[先决条件](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)。

## <a name="unable-to-encrypt"></a>无法加密

在某些情况下，Linux 磁盘加密看上去停滞在“OS 磁盘加密已启动”状态，同时 SSH 处于禁用状态。 加密过程可能需要 3-16 小时才能完成存储库映像。 如果添加了多 TB 大小的数据磁盘，此过程可能需要数天才能完成。

Linux OS 磁盘加密序列暂时卸载 OS 驱动器。 然后，它将对整个 OS 磁盘进行逐块加密，然后再将其重新安装为加密状态。 与 Windows 上的 Azure 磁盘加密不同，Linux 磁盘加密不允许在加密的同时并发使用 VM。 VM 的性能特点会在完成加密所需的时间上产生显著差异。 这些特点包括磁盘大小以及存储帐户为标准还是高级 (SSD) 存储。

若要检查加密状态，可以轮询 [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) 命令返回的 ProgressMessage 字段。 加密 OS 驱动器时，VM 会进入维护状态，同时会禁用 SSH，以防止对进行中的进程造成任何干扰。 进行加密时，EncryptionInProgress 消息大部分时间都在提供报告。 几个小时之后，VMRestartPending 消息会提示重新启动 VM。 例如：


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

系统会提示重新启动 VM，VM 重启之后，必须等待 2-3 分钟，以重新启动并在目标上执行最终步骤。 当加密最终完成时，状态消息会发生更改。 显示此消息后，加密的 OS 驱动器预期可供使用，并且 VM 可恢复使用。

在以下情况下，建议将 VM还原到就在加密前一刻获取的快照或备份：
   - 如果上述的重新启动序列不会发生。
   - 如果启动信息、进度消息或其他错误指示器报告 OS 加密过程已失败。 本指南中以“卸载失败”错误为例，介绍了一个消息。

在下次尝试加密之前，请重新评估 VM 的特征，并确保满足所有先决条件。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>防火墙保护下的 Azure 磁盘加密故障排除
如果连接受到防火墙、代理要求或网络安全组 (NSG) 设置的限制，扩展执行所需任务的能力可能会受到干扰。 此干扰可能会导致出现类似于“VM 上未提供扩展状态”的状态消息。 在预期方案中，将无法完成加密。 以下部分描述了可能需要调查的一些常见防火墙问题。

### <a name="network-security-groups"></a>网络安全组
应用的任何网络安全组设置仍必须允许终结点满足所述的与磁盘加密相关的网络配置[先决条件](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites)。

### <a name="azure-key-vault-behind-a-firewall"></a>防火墙保护下的 Azure Key Vault
VM 必须能够访问 Key Vault。 请参阅有关从 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) 团队维护的防火墙后面访问 Key Vault 的指导。

### <a name="linux-package-management-behind-a-firewall"></a>防火墙保护下的 Linux 程序包管理

在运行时，启用加密之前，适用于 Linux 的 Azure 磁盘加密依赖于目标分发版的程序包管理系统来安装所需的必备组件。 如果防火墙设置阻止 VM 下载并安装这些组件，则后续的失败就在意料之中。 配置此程序包管理系统的步骤会因分发版而异。 在 Red Hat 上，如果需要代理，则必须确保正确设置订阅管理器和 yum。 有关详细信息，请参阅[如何排除有关订阅管理器和 yum 的问题](https://access.redhat.com/solutions/189533)。  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core 疑难解答

在 Windows Server 2016 Server core 上，bdehdcfg 组件默认不可用。 Azure 磁盘加密需要此组件。 它用于从 OS 卷拆分系统卷，该操作在虚拟机生命期内只进行一次。 在后面的加密操作中，不需要这些二进制文件。

若要暂时避开此问题，请将下面 Windows Server 2016 Data Center VM 中的 4 个文件复制到 Server Core 上的同一位置中：

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. 输入以下命令：

   ```
   bdehdcfg.exe -target default
   ```

   3. 此命令将创建一个 550 MB 系统分区。 重新启动系统。

   4. 使用 DiskPart 检查卷，然后继续。  

例如：

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="next-steps"></a>后续步骤

本文档已详细描述有关 Azure 磁盘加密的一些常见问题和解决这些问题的方法。 有关此服务及其功能的详细信息，请参阅以下文章：

- [在 Azure 安全中心应用磁盘加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [加密 Azure 虚拟机](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure 静态数据加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

