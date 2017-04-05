---
title: "社区工具 - 将经典资源移动至 Azure Resource Manager | Microsoft Docs"
description: "本文编录了社区提供的工具，这些工具适用于将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5e2ad5e5eae97645368797c8cdf848d88719bb64
ms.lasthandoff: 03/29/2017


---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>使用社区工具将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager
本文编录了社区提供的工具，这些工具适用于将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。

> [!NOTE]
> Microsoft 支持服务并不正式支持这些工具。 因此 GitHub 上提供了其开放源代码，我们很乐意接受有关修复或其他方案的 PR。 若要报告问题，请使用 GitHub 问题功能。
> 
> 使用这些工具进行迁移会造成经典虚拟机停机。 若要了解平台支持的迁移，请访问 
> 
> * [平台支持从经典部署模型迁移到 Azure Resource Manager 堆栈的 IaaS 资源](virtual-machines-windows-migration-classic-resource-manager.md)
> * [有关平台支持的从经典部署模型到 Azure Resource Manager 的迁移的技术深入探讨](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
这是一个 PowerShell 脚本模块，用于将**单个**虚拟机 (VM) 从经典部署模型堆栈迁移到 Azure Resource Manager 部署模型。 

[工具文档链接](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz 是另一选项，用于将整套经典部署模型 IaaS 资源迁移到 Azure Resource Manager IaaS 资源。 可以在同一订阅中迁移，也可以在不同订阅和不同订阅类型（例如 CSP 订阅）中迁移。

[工具文档链接](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)


