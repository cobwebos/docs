---
title: "虚拟机扩展和功能 | Microsoft Docs"
description: "了解可为 Azure 虚拟机提供哪些扩展，这些虚拟机扩展按它们提供或改进的功能进行分组。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 7bd48e2ef9177369190494c38bfdcf1bb99e61ea


---
# <a name="about-virtual-machine-extensions-and-features"></a>关于虚拟机扩展和功能
## <a name="azure-vm-extensions"></a>Azure VM 扩展
Azure 虚拟机扩展是小型应用程序，可在Azure 虚拟机上提供部署后配置和自动化任务。 例如，如果虚拟机要求安装软件、防病毒保护或 Docker 配置，便可以使用 VM 扩展来完成这些任务。 可以使用 Azure CLI、PowerShell、Resource Manager 模板和 Azure 门户运行 Azure VM 扩展。 扩展可与新虚拟机部署捆绑在一起，或者针对任何现有系统运行。

本文档提供 Azure 虚拟机扩展的先决条件，以及有关如何检测可用 VM 扩展的指导。 

## <a name="azure-vm-agent"></a>Azure VM 代理
Azure VM 代理可管理 Azure 虚拟机与 Azure 结构控制器之间的交互。 VM 代理负责部署和管理 Azure 虚拟机的许多功能层面，包括运行 VM 扩展。 Azure VM 代理预先安装在 Azure 库映像上，并可安装在支持的操作系统上。 

有关支持的操作系统和安装说明，请参阅 [Azure 虚拟机代理](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="discover-vm-extensions"></a>发现 VM 扩展
有许多不同的 VM 扩展可与 Azure 虚拟机配合使用。 若要查看完整列表，请使用 Azure CLI 运行以下命令，并将命令中的位置替换为所选位置。

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>常见的 VM 扩展
| 扩展名称 | 说明 | 更多信息 |
| --- | --- | --- |
| 适用于 Windows 的自定义脚本扩展 |针对 Azure 虚拟机运行脚本 |[适用于 Windows 的自定义脚本扩展](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 适用于 Windows 的 DSC 扩展 |PowerShell DSC (Desired State Configuration) 扩展。 |[Docker VM 扩展](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure 诊断扩展 |管理 Azure 诊断 |[Azure 诊断扩展](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |




<!--HONumber=Nov16_HO3-->


