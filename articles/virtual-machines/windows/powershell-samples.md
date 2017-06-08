---
title: "Azure 虚拟机 PowerShell 示例 | Microsoft 文档"
description: "Azure 虚拟机 PowerShell 示例"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/16/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c57bf0efc1eb97d34d76483b8889d884ccfd0988
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 虚拟机 PowerShell 示例

下表包含用于创建和管理 Windows 虚拟机的 PowerShell 脚本示例的链接。

| | |
|---|---|
|**创建虚拟机**||
| [创建完全配置的虚拟机](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建资源组、虚拟机以及所有相关资源。|
| [创建 VM 并运行配置脚本](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 IIS。 |
| [创建 VM 并运行 DSC 配置](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建一个虚拟机，并使用 Azure Desired State Configuration (DSC) 扩展来安装 IIS。 |
| [从托管 OS 磁盘创建 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 通过将现有托管磁盘附加为 OS 磁盘来创建虚拟机。 |
| [从快照创建 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 通过先从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 |
|**监视虚拟机**||
| [使用 Operations Management Suite 监视 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 创建一个虚拟机，安装 Operations Management Suite 代理，并在 OMS 工作区中注册该 VM。  |
| | |

