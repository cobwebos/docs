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
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418397"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>将 Azure VM 迁移到 Azure 中的托管磁盘

Azure 托管磁盘无需单独管理存储帐户，从而简化了存储管理。  还可以将现有的 Azure VM 迁移到托管磁盘，以利用可用性集中更好的 VM 可靠性。 它可确保可用性集中不同 Vm 的磁盘彼此之间完全隔离以避免单一故障点。 它会自动将可用性集中不同 VM 的磁盘置于不同的存储缩放单元（戳），限制由于硬件和软件故障引起的单个存储缩放单元故障影响。
可根据需要，从存储选项的四种类型中进行选择。 若要了解可用的磁盘类型，请参阅[选择磁盘类型](disks-types.md)一文

## <a name="migration-scenarios"></a>迁移方案

可在以下方案中迁移到托管磁盘：

|场景  |文章  |
|---------|---------|
|将独立的 VM 和可用性集中的 VM 转换为托管磁盘     |[转换 VM 以使用托管磁盘](convert-unmanaged-to-managed-disks.md)         |
|转换单个 VM 从经典部署模型到 Resource Manager 将托管磁盘上     |[从经典 VHD 创建 VM](create-vm-specialized-portal.md)         |
|将转换在 vNet 中的所有 Vm 从经典到资源管理器将托管磁盘上     |[将 IaaS 资源从经典迁移到 Resource Manager](migration-classic-resource-manager-ps.md)，然后[将 VM 从非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)         |
|升级与标准的非托管磁盘的 vm 使用托管的高级磁盘的 Vm     | 首先，[将 Windows 虚拟机从非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)。 然后[更新托管磁盘的存储类型](convert-disk-storage.md)。         |

## <a name="next-steps"></a>后续步骤

- 详细了解[托管磁盘](managed-disks-overview.md)
- 查看[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。
