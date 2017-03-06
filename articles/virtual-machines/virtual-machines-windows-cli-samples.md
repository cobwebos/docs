---
title: "Azure CLI 示例 Windows | Microsoft 文档"
description: "Azure CLI 示例 Windows"
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
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cfaec3517dc490d48cb989e28d2c8d3a2bad84
ms.openlocfilehash: 7003225447425d239cfa75852b5c83bdab97b93b
ms.lasthandoff: 03/01/2017


---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>适用于 Windows 虚拟机的 Azure CLI 示例

下表包含指向使用部署 Windows 虚拟机的 Azure CLI 生成的 bash 脚本的链接。

| | |
|---|---|
|**创建虚拟机**||
| [创建虚拟机](./scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 使用最小配置创建 Windows 虚拟机。 |
| [创建完全配置的虚拟机](./scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建资源组、虚拟机以及所有相关资源。|
| [创建高度可用的虚拟机](./scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 使用高度可用且负载均衡的配置创建多个虚拟机。 |
| [创建 VM 并运行配置脚本](./scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 创建一个虚拟机，并使用 Azure 自定义脚本扩展安装 IIS。 |
| [创建 VM 并运行 DSC 配置](./scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，并使用 Azure Desired State Configuration (DSC) 扩展来安装 IIS。 |
|**网络虚拟机**||
| [保护虚拟机之间的网络流量](./scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建两个虚拟机、所有相关资源以及内部和外部网络安全组 (NSG)。 |
|**监视虚拟机**||
| [使用 Operations Management Suite 监视 VM](./scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 创建一个虚拟机，安装 Operations Management Suite 代理，并在 OMS 工作区中注册该 VM。  |
| | |

