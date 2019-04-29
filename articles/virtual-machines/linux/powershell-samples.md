---
title: Azure 虚拟机 PowerShell 示例 | Microsoft 文档
description: Azure 虚拟机 PowerShell 示例
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 53784c3d74f9e6af5f1e84cc098194113e81333b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771254"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 虚拟机 PowerShell 示例

下表包含用于创建和管理 Linux 虚拟机的 PowerShell 脚本示例的链接。

| | |
|---|---|
|**创建虚拟机**||
| [创建完全配置的虚拟机](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 创建资源组、虚拟机以及所有相关资源。|
| [创建已启用 Docker 的 VM](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 创建一个虚拟机，将此 VM 配置为 Docker 主机，并运行 NGINX 容器。 |
| [创建 VM 并运行配置脚本](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 NGINX。 |
| [创建安装有 WordPress 的 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 WordPress。 |
| [从托管 OS 磁盘创建 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 通过将现有托管磁盘附加为 OS 磁盘来创建虚拟机。 |
| [从快照创建 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 通过先从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 |
|**管理存储**||
| [在相同或不同的订阅中从 VHD 创建托管磁盘](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 在相同或不同订阅中，从专用 VHD 将托管磁盘创建为 OS 磁盘，或从数据 VHD 将托管磁盘创建为数据磁盘。  |
| [从快照创建托管磁盘](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 从快照创建托管磁盘。 |
| [将快照作为 VHD 导出到存储帐户](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 将托管快照作为 VHD 导出到不同区域中的存储帐户。 |
| [将托管磁盘的 VHD 导出到存储帐户](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 将托管磁盘的基础 VHD 导出到不同区域的存储帐户。 |
| [从 VHD 创建快照](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 从 VHD 创建快照，然后使用该快照快速创建多个相同的托管磁盘。  |
| [将快照复制到相同或不同的订阅](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 将快照复制到父快照所在区域中的相同或不同订阅。 |
|**监视虚拟机**||
| [使用 Azure Monitor 日志监视 VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 创建一个虚拟机，安装 Log Analytics 代理，并在 Log Analytics 工作区中注册该 VM。  |
| [将托管磁盘复制到相同或不同的订阅](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 将托管磁盘复制到父托管磁盘所在区域中的相同或不同订阅。
| | |
