---
title: 适用于虚拟机和虚拟机规模集的 Azure 磁盘加密
description: 了解针对虚拟机的 Azure 磁盘加密 (Vm) 和 VM 规模集。 Azure 磁盘加密适用于 Linux 和 Windows Vm。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 21194bf2fe76a7eb0ee034d4a502c20ee3032dd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543667"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>适用于虚拟机和虚拟机规模集的 Azure 磁盘加密

Azure 磁盘加密可同时适用于 Linux 和 Windows 虚拟机以及虚拟机规模集。 

## <a name="linux-virtual-machines"></a>Linux 虚拟机

以下文章提供了有关加密 Linux 虚拟机的指南。

### <a name="current-version-of-azure-disk-encryption"></a>Azure 磁盘加密的当前版本

- [Linux 虚拟机的 Azure 磁盘加密概述](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux VM 上的 Azure 磁盘加密方案](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure CLI 创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [使用 Azure Powershell 创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [使用 Azure 门户创建和加密 Linux VM](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [适用于 Linux 的 Azure 磁盘加密扩展架构](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [创建和配置用于 Azure 磁盘加密的密钥保管库](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure 磁盘加密示例脚本](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure 磁盘加密疑难解答](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure 磁盘加密常见问题解答](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>使用 Azure AD 执行 Azure 磁盘加密（以前版本）

- [使用 Azure AD 对 Linux 虚拟机执行 Azure 磁盘加密概述](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- 了解[在 Linux VM 上使用 Azure AD 执行 Azure 磁盘加密的方案](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows 虚拟机

以下文章提供了有关加密 Windows 虚拟机的指南。

### <a name="current-version-of-azure-disk-encryption"></a>Azure 磁盘加密的当前版本

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

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>使用 Azure AD 执行 Azure 磁盘加密（以前版本）

- [使用 Azure AD 对 Windows 虚拟机执行 Azure 磁盘加密概述](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- 了解[在 Windows VM 上使用 Azure AD 执行 Azure 磁盘加密的方案](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure AD 创建和配置用于 Azure 磁盘加密的密钥保管库（以前版本）](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

以下文章提供了有关加密虚拟机规模集的指南。

- [适用于虚拟机规模集的 Azure 磁盘加密概述](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [使用 Azure CLI 加密虚拟机规模集](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [使用 Azure Powershell 加密虚拟机规模集](../../virtual-machine-scale-sets/disk-encryption-powershell.md)。
- [使用 Azure 资源管理器加密虚拟机规模集](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [将 Azure 磁盘加密与虚拟机规模集扩展排序配合使用](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>后续步骤

- [Azure 加密概述](encryption-overview.md)
- [静态数据加密](encryption-atrest.md)
- [数据安全与加密最佳做法](data-encryption-best-practices.md)
