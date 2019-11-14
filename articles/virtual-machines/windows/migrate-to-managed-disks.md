---
title: 将 Azure Vm 迁移到托管磁盘
description: 迁移在存储帐户中使用非托管磁盘创建的 Azure 虚拟机，以使用托管磁盘。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b907cf5bf64aa1a8a458a8d2b42c30dffd0dfd1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74029794"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>将 Azure VM 迁移到 Azure 中的托管磁盘

Azure 托管磁盘无需单独管理存储帐户，从而简化了存储管理。  还可以将现有的 Azure VM 迁移到托管磁盘，以利用可用性集中更好的 VM 可靠性。 这可确保将可用性集中不同 VM 的磁盘最大限度地彼此独立，以避免单一故障点。 它会自动将可用性集中不同 VM 的磁盘置于不同的存储缩放单元（戳），限制由于硬件和软件故障引起的单个存储缩放单元故障影响。
可根据需要，从存储选项的四种类型中进行选择。 若要了解可用的磁盘类型，请参阅[选择磁盘类型](disks-types.md)一文

## <a name="migration-scenarios"></a>迁移方案

可以在以下方案中迁移到托管磁盘：

|方案  |文章  |
|---------|---------|
|将独立的 VM 和可用性集中的 VM 转换为托管磁盘     |[转换 VM 以使用托管磁盘](convert-unmanaged-to-managed-disks.md)         |
|将托管磁盘上的单个 VM 从经典部署模型转换为资源管理器部署模型     |[从经典 VHD 创建 VM](create-vm-specialized-portal.md)         |
|将 vNet 中的所有 VM 从经典部署模型转换为托管磁盘上的资源管理器部署模型     |[将 IaaS 资源从经典迁移到 Resource Manager](migration-classic-resource-manager-ps.md)，然后[将 VM 从非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)         |
|将具有标准非托管磁盘的 VM 升级到具有托管高级磁盘的 VM     | 首先，[将 Windows 虚拟机从非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)。 然后[更新托管磁盘的存储类型](convert-disk-storage.md)。         |

## <a name="next-steps"></a>后续步骤

- 详细了解[托管磁盘](managed-disks-overview.md)
- 查看[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。
