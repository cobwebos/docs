---
title: 优化 Azure Lsv2 系列虚拟机的性能 - 存储
description: 了解如何在 Lsv2 系列虚拟机上优化解决方案的性能。
services: virtual-machines-windows
author: sasha-melamed
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 57b248908a02327f2521be05920259681a26817a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920223"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>优化 Lsv2 系列虚拟机的性能

Lsv2 系列虚拟机支持各种工作负载，这些工作负载需要跨各种应用程序和行业的本地存储实现高 I/O 和吞吐量。  Lsv2 系列是大数据、SQL、NoSQL 数据库、数据仓库和大型事务数据库（包括卡桑德拉、蒙戈DB、Cloudera 和 Redis）的理想选择。

Lsv2 系列虚拟机 （VM） 的设计使 AMD EPYC™ 7551 处理器最大化，从而在处理器、内存、NVMe 设备和 VM 之间提供最佳性能。 除了最大化硬件性能外，Lsv2 系列 VM 还旨在满足 Windows 和 Linux 操作系统的需求，从而在硬件和软件方面实现更好的性能。

调整软件和硬件后[，Windows Server 2019 数据中心](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)的优化版本于 2018 年 12 月初发布给 Azure 应用商店，支持 Lsv2 系列 VM 中的 NVMe 设备的最大性能。

本文提供提示和建议，以确保工作负载和应用程序实现在 VM 中设计的最大性能。 随着更多 Lsv2 优化映像添加到 Azure 应用商店，此页面上的信息将不断更新。

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™芯片组架构

Lsv2 系列 VM 使用基于 Zen 微架构的 AMD EYPC ™服务器处理器。 AMD 为 EYPC 开发了无限交换矩阵 （IF™ 作为其 NUMA 型号的可扩展互连，可用于模上、封装和多包通信。 与英特尔现代单片式处理器中使用的 QPI（快速路径互连）和 UPI（超路径互连）相比，AMD 的多 NUMA 小片式架构既可能带来性能优势，也可能带来挑战。 内存带宽和延迟限制的实际影响可能因运行的工作负载类型而异。

## <a name="tips-for-maximizing-performance"></a>最大化性能的提示

* 为 Lsv2 系列 VM 供电的硬件使用具有八个 I/O 队列对 （QP） 的 NVMe 设备。 每个 NVMe 设备 I/O 队列实际上是一对：提交队列和完成队列。 NVMe 驱动程序通过循环计划中分发 I/O 来优化这八个 I/O 季度的利用率。 为了获得最大性能，每个设备运行八个作业以匹配。

* 避免在活动工作负载期间将 NVMe 管理命令（例如，NVMe SMART 信息查询等）与 NVMe I/O 命令混合。 Lsv2 NVMe 设备由 Hyper-V NVMe 直接技术支持，该技术在任何 NVMe 管理命令挂起时都会切换到"慢速模式"。 如果发生这种情况，Lsv2 用户可能会看到 NVMe I/O 性能的显著性能下降。

* Lsv2 用户不应依赖从 VM 中报告的设备 NUMA 信息（所有 0）来处理数据驱动器，以决定其应用的 NUMA 相关性。 如果可能，建议用于提高性能的方法是跨 CPU 扩展工作负载。 

* Lsv2 VM NVMe 设备每个 I/O 队列对受支持的最大队列深度为 1024（与 Amazon i3 QD 32 限制）。 Lsv2 用户应将其（综合）基准工作负荷限制为队列深度 1024 或更低，以避免触发队列完全条件，这可能会降低性能。

## <a name="utilizing-local-nvme-storage"></a>利用本地 NVMe 存储

所有 Lsv2 VM 上的 1.92 TB NVMe 磁盘上的本地存储是短暂的。 在 VM 成功标准重新启动期间，本地 NVMe 磁盘上的数据将保持不变。 如果重新部署、取消分配或删除 VM，则该数据将不会保留在 NVMe 上。 如果另一个问题导致 VM 或正在运行的硬件不正常，则数据不会持久化。 发生这种情况时，旧主机上的所有数据将安全地擦除。

有时，VM 需要移动到其他主机，例如，在计划维护操作期间。 计划中的维护操作和一些硬件故障可以预期与[计划事件](scheduled-events.md)。 应使用计划事件来更新任何预测的维护和恢复操作。

如果计划中的维护事件要求在具有空本地磁盘的新主机上重新创建 VM，则需要重新同步数据（同样，旧主机上的任何数据都将安全擦除）。 这是因为 Lsv2 系列 VM 当前不支持本地 NVMe 磁盘上的实时迁移。

有两种模式用于计划维护。

### <a name="standard-vm-customer-controlled-maintenance"></a>标准 VM 客户控制维护

- 在 30 天的时段内，VM 将移动到更新的主机。
- Lsv2 本地存储数据可能会丢失，因此建议在事件发生之前备份数据。

### <a name="automatic-maintenance"></a>自动维护

- 如果客户不执行客户控制的维护，或者在发生紧急程序（如安全零日事件）时发生。
- 旨在保留客户数据，但存在 VM 冻结或重新启动的小风险。
- Lsv2 本地存储数据可能会丢失，因此建议在事件发生之前备份数据。

对于任何即将发生的服务事件，请使用受控维护过程选择最方便的时间进行更新。 在活动之前，您可以将数据备份到高级存储中。 维护事件完成后，您可以将数据返回到刷新的 Lsv2 VR 本地 NVMe 存储。

维护本地 NVMe 磁盘上数据的方案包括：

- VM 正在运行且正常。
- VM 已就地重新启动（由您或 Azure 重新启动）。
- VM 已暂停（未取消分配而停止）。
- 大多数计划的维护维修操作。

安全擦除数据以保护客户的方案包括：

- VM 被重新部署、停止（取消分配）或删除（由您删除）。
- VM 变得不正常，由于硬件问题，必须将服务修复到另一个节点。
- 少数计划维护服务操作，这些操作要求将 VM 重新分配到另一个主机进行维修。

要了解有关在本地存储中备份数据的选项的详细信息，请参阅[Azure IaaS 磁盘的备份和灾难恢复](backup-and-disaster-recovery-for-azure-iaas-disks.md)。

## <a name="frequently-asked-questions"></a>常见问题

* **如何开始部署 Lsv2 系列 VM？**  
   与任何其他 VM 一样，请使用[门户](quick-create-portal.md) [、Azure CLI](quick-create-cli.md)或[PowerShell](quick-create-powershell.md)创建 VM。

* **单个 NVMe 磁盘故障是否会导致主机上的所有 VM 失败？**  
   如果在硬件节点上检测到磁盘故障，则硬件处于故障状态。 发生这种情况时，节点上的所有 VM 将自动取消分配并移动到正常节点。 对于 Lsv2 系列 VM，这意味着客户在故障节点上的数据也会被安全地擦除，并且需要客户在新节点上重新创建。 如前所述，在 Lsv2 上提供实时迁移之前，故障节点上的数据将在 VM 传输到另一个节点时主动移动。

* **我是否需要在 Windows 服务器 2012 或 Windows 服务器 2016 中的 Windows 中进行轮询调整？**  
   NVMe 轮询仅在 Azure 上的 Windows Server 2019 上可用。  

* **我可以切换回传统的中断服务例程 （ISR） 模型吗？**  
   Lsv2 系列 VM 针对 NVMe 轮询进行了优化。 不断提供更新以提高轮询性能。

* **我可以调整 Windows Server 2019 中的轮询设置吗？**  
   轮询设置不可用户调整。
   
## <a name="next-steps"></a>后续步骤

* 查看针对 Azure 上的[存储性能优化](sizes-storage.md)的所有 VM 的规范
