---
title: 适用于虚拟机和虚拟机规模集的 Azure 磁盘加密
description: 本文提供 Azure 磁盘加密概述
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599950"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>适用于虚拟机和虚拟机规模集的 Azure 磁盘加密

Azure 磁盘加密可同时适用于 Linux 和 Windows 虚拟机以及虚拟机规模集。 

## <a name="linux-virtual-machines"></a>Linux 虚拟机

以下文章提供了有关加密 Linux 虚拟机的指南。

### <a name="current-version-of-azure-disk-encryption"></a>Azure 磁盘加密的当前版本

- [适用于 Linux 虚拟机的 Azure 磁盘加密概述](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux Vm 上的 Azure 磁盘加密方案](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure CLI 创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [使用 Azure 门户创建并加密 Linux VM](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure 磁盘加密示例脚本](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure 磁盘加密故障排除](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure 磁盘加密常见问题解答](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁盘加密与 Azure AD （以前的版本）

- [适用于 Linux 虚拟机的 Azure AD 的 Azure 磁盘加密概述](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Linux Vm 上的 Azure AD 方案的 Azure 磁盘加密](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure AD 为 Azure 磁盘加密创建和配置密钥保管库（以前的版本）](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows 虚拟机

以下文章提供了有关加密 Windows 虚拟机的指南。

### <a name="current-version-of-azure-disk-encryption"></a>Azure 磁盘加密的当前版本

- [适用于 Windows 虚拟机的 Azure 磁盘加密概述](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows Vm 上的 Azure 磁盘加密方案](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure CLI 创建和加密 Windows VM](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 创建和加密 Windows VM](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [使用 Azure 门户创建和加密 Windows VM](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure 磁盘加密示例脚本](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure 磁盘加密故障排除](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure 磁盘加密常见问题解答](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁盘加密与 Azure AD （以前的版本）

- [Windows 虚拟机 Azure AD 的 Azure 磁盘加密概述](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Windows Vm 上的 Azure AD 方案的 Azure 磁盘加密](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure AD 为 Azure 磁盘加密创建和配置密钥保管库（以前的版本）](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

以下文章提供了对虚拟机规模集进行加密的指导。

- [虚拟机规模集的 Azure 磁盘加密概述](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [使用 Azure CLI 加密虚拟机规模集](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [使用 Azure Powershell 对虚拟机规模集进行加密](../../virtual-machine-scale-sets/disk-encryption-powershell.md)。
- [使用 Azure 资源管理器加密虚拟机规模集](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [将 Azure 磁盘加密用于虚拟机规模集扩展顺序](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>后续步骤

- [Azure 加密概述](encryption-overview.md)
- [静态数据加密](encryption-atrest.md)
- [数据安全与加密最佳实践](data-encryption-best-practices.md)
