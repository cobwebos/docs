---
title: 将 Azure VM 迁移到托管磁盘 | Microsoft Docs
description: 迁移在存储帐户中使用非托管磁盘创建的 Azure 虚拟机，以使用托管磁盘。
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12cd1caa4cb96dbd5862776589d4a34aeb294ca1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689719"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>将 Azure VM 迁移到 Azure 中的托管磁盘

Azure 托管磁盘无需单独管理存储帐户，从而简化了存储管理。  还可以将现有的 Azure VM 迁移到托管磁盘，以利用可用性集中更好的 VM 可靠性。 这可确保一个可用性集中不同 VM 的磁盘可充分地彼此隔离，避免出现单一故障点。 它会自动将可用性集中不同 VM 的磁盘置于不同的存储缩放单元（戳），限制由于硬件和软件故障引起的单个存储缩放单元故障影响。
可根据需要，从存储选项的四种类型中进行选择。 若要了解可用的磁盘类型，请参阅[选择磁盘类型](disks-types.md)一文

## <a name="migrate-scenarios"></a>迁移方案

可在以下方案中迁移到托管磁盘：

| **迁移...**                                            | **文档链接**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 将独立的 VM 和可用性集中的 VM 转换为托管磁盘   | [转换 VM 以使用托管磁盘](convert-unmanaged-to-managed-disks.md) |
| 单个 VM 从经典迁移到托管磁盘上的 Resource Manager     | [从经典 VHD 创建 VM](create-vm-specialized-portal.md)  | 
| vNet 中的所有 VM 从经典迁移到托管磁盘上的 Resource Manager     | [将 IaaS 资源从经典迁移到 Resource Manager](migration-classic-resource-manager-ps.md)，然后[将 VM 从非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>后续步骤

- 详细了解[托管磁盘](managed-disks-overview.md)
- 查看[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。
