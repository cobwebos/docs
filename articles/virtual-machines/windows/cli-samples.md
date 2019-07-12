---
title: Azure CLI 示例 Windows | Microsoft 文档
description: Azure CLI 示例 Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f0fca7a36caa84d22e07dad01089209752efa6fa
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719054"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>适用于 Windows 虚拟机的 Azure CLI 示例

下表包含指向使用部署 Windows 虚拟机的 Azure CLI 生成的 bash 脚本的链接。

| | |
|---|---|
|**创建虚拟机**||
| [创建虚拟机](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 使用最小配置创建 Windows 虚拟机。 |
| [创建完全配置的虚拟机](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建资源组、虚拟机以及所有相关资源。|
| [创建高度可用的虚拟机](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 使用高度可用且负载均衡的配置创建多个虚拟机。 |
| [创建 VM 并运行配置脚本](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 IIS。 |
| [创建 VM 并运行 DSC 配置](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，并使用 Azure Desired State Configuration (DSC) 扩展来安装 IIS。 |
|**管理存储**||
| [从 VHD 创建托管磁盘](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 从作为 OS 磁盘的专用 VHD 或从作为数据磁盘的数据 VHD 创建托管磁盘。  |
| [从快照创建托管磁盘](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 从快照创建托管磁盘。 |
| [将托管磁盘复制到相同或不同订阅](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 将托管磁盘复制到相同或不同订阅（但是与父托管磁盘处于相同区域中）。 
| [将快照作为 VHD 导出到存储帐户](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 将托管快照作为 VHD 导出到不同区域中的存储帐户。 |
| [将托管磁盘的 VHD 导出到存储帐户](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 将托管磁盘的基础 VHD 导出到不同区域的存储帐户。 |
| [将快照复制到相同或不同订阅](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 将快照复制到相同或不同订阅（但是与父快照处于相同区域中）。 |
|**网络虚拟机**||
| [保护虚拟机之间的网络流量](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建两个虚拟机、所有相关资源以及内部和外部网络安全组 (NSG)。 |
|**保护虚拟机**||
| [对 VM 和数据磁盘进行加密](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建 Azure Key Vault、加密密钥和服务主体，然后对 VM 进行加密。 |
|**监视虚拟机**||
| [使用 Azure Monitor 监视 VM](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，安装 Log Analytics 代理，并在 Log Analytics 工作区中注册该 VM。  |
| | |
