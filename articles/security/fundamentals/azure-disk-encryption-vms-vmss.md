---
title: 虚拟机和虚拟机缩放集的 Azure 磁盘加密
description: 本文概述 Azure 磁盘加密
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062129"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>虚拟机和虚拟机缩放集的 Azure 磁盘加密

Azure 磁盘加密可以应用于 Linux 和 Windows 虚拟机，以及虚拟机缩放集。 

## <a name="linux-virtual-machines"></a>Linux 虚拟机

以下文章提供了加密 Linux 虚拟机的指导。

### <a name="current-version-of-azure-disk-encryption"></a>当前版本的 Azure 磁盘加密

- [Linux 虚拟机的 Azure 磁盘加密概述](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux VM 上的 Azure 磁盘加密方案](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure CLI 创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [使用 Azure 门户创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Linux 的 Azure 磁盘加密扩展架构](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [创建和配置用于 Azure 磁盘加密的密钥保管库](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure 磁盘加密示例脚本](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure 磁盘加密疑难解答](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure 磁盘加密常见问题解答](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁盘加密与 Azure AD（早期版本）

- [Linux 虚拟机使用 Azure AD 进行 Azure 磁盘加密概述](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [在 Linux VM 上使用 Azure AD 方案进行 Azure 磁盘加密](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows 虚拟机

以下文章提供了加密 Windows 虚拟机的指导。

### <a name="current-version-of-azure-disk-encryption"></a>当前版本的 Azure 磁盘加密

- [Windows 虚拟机的 Azure 磁盘加密概述](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows VM 上的 Azure 磁盘加密方案](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure CLI 创建和加密 Windows VM](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 创建和加密 Windows VM](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [使用 Azure 门户创建和加密 Windows VM](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [适用于 Windows 的 Azure 磁盘加密扩展架构](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [创建和配置用于 Azure 磁盘加密的密钥保管库](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure 磁盘加密示例脚本](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure 磁盘加密疑难解答](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure 磁盘加密常见问题解答](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁盘加密与 Azure AD（早期版本）

- [Windows 虚拟机使用 Azure AD 进行 Azure 磁盘加密概述](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Windows VM 上具有 Azure AD 方案的 Azure 磁盘加密](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

以下文章提供了加密虚拟机规模集的指导。

- [虚拟机规模集的 Azure 磁盘加密概述](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [使用 Azure CLI 加密虚拟机规模集](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [使用 Azure Powershell 加密虚拟机缩放集](../../virtual-machine-scale-sets/disk-encryption-powershell.md)。
- [使用 Azure 资源管理器加密虚拟机规模集](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [将 Azure 磁盘加密与虚拟机规模集扩展排序配合使用](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>后续步骤

- [Azure 加密概述](encryption-overview.md)
- [静态数据加密](encryption-atrest.md)
- [数据安全与加密最佳实践](data-encryption-best-practices.md)
