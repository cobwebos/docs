---
title: Azure 磁盘加密故障排除指南
description: 本文提供适用于 Windows VM 的 Microsoft Azure 磁盘加密的故障排除提示。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 45c64261b60d88557e63cb8f9af922fd7aa71ad4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977895"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁盘加密故障排除指南

本指南面向使用 Azure 磁盘加密的组织中的 IT 专业人员、信息安全分析人员和云管理员。 本文旨在帮助排查与磁盘加密相关的问题。

在执行以下任何步骤之前，请首先确保你尝试加密的 VM 具有[受支持的 VM 大小和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)，并且已满足所有先决条件：

- [网络要求](disk-encryption-overview.md#networking-requirements)
- [组策略要求](disk-encryption-overview.md#group-policy-requirements)
- [加密密钥存储要求](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>防火墙保护下的 Azure 磁盘加密故障排除

如果连接受到防火墙、代理要求或网络安全组 (NSG) 设置的限制，扩展执行所需任务的能力可能会受到干扰。 此干扰可能会导致出现类似于“VM 上未提供扩展状态”的状态消息。 在预期方案中，将无法完成加密。 以下部分描述了可能需要调查的一些常见防火墙问题。

### <a name="network-security-groups"></a>网络安全组
应用的任何网络安全组设置仍必须允许终结点满足所述的与磁盘加密相关的网络配置[先决条件](disk-encryption-overview.md#networking-requirements)。

### <a name="azure-key-vault-behind-a-firewall"></a>防火墙保护下的 Azure Key Vault

使用 [Azure AD 凭据](disk-encryption-windows-aad.md#)启用加密时，目标 VM 必须允许连接到 Azure Active Directory 终结点和密钥保管库终结点。 当前 Azure Active Directory 身份验证终结点保留在 [Microsoft 365 url 和 IP 地址范围](/microsoft-365/enterprise/urls-and-ip-address-ranges) 文档的56和59部分中。 在有关如何[访问防火墙保护下的 Azure 密钥保管库](../../key-vault/general/access-behind-firewall.md)的文档中提供了密钥保管库说明。

### <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务 
VM 必须能够访问这样的 [Azure 实例元数据服务](../windows/instance-metadata-service.md)终结点：该终结点使用只能从 VM 内访问的已知不可路由 IP 地址 (`169.254.169.254`)。  不支持将本地 HTTP 流量更改为此地址的代理配置（例如，添加 X-Forwarded-For 标头）。

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

## <a name="troubleshooting-encryption-status"></a>加密状态故障排除 

即使磁盘在 VM 中解密后，门户也可能显示该磁盘已加密。  当使用低级别命令从 VM 内部直接解密磁盘，而不是使用较高级别的 Azure 磁盘加密管理命令时，可能会发生这种情况。  较高级别命令不仅从 VM 内部解密磁盘，而且在 VM 外部它们还更新与 VM 关联的重要平台级加密设置和扩展设置。  如果这些未保持一致，平台将无法报告加密状态或正确预配 VM。

若要使用 PowerShell 禁用 Azure 磁盘加密，请使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)，然后使用 [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension)。 禁用加密之前，运行 Remove-AzVMDiskEncryptionExtension 会失败。

若要使用 CLI 禁用 Azure 磁盘加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption)。 

## 



## <a name="next-steps"></a>后续步骤

本文档已详细描述有关 Azure 磁盘加密的一些常见问题和解决这些问题的方法。 有关此服务及其功能的详细信息，请参阅以下文章：

- [在 Azure 安全中心应用磁盘加密](../../security-center/asset-inventory.md)
- [Azure 静态数据加密](../../security/fundamentals/encryption-atrest.md)