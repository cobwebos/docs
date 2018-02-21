---
title: "Azure 存储中断时怎么办 | Microsoft Docs"
description: "Azure 存储中断时怎么办"
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: 327816874fb2f2d43192e02410bd31c168e574f7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>在 Azure 存储中断时该怎么办
Microsoft 一直努力确保所提供的服务始终可用。 但有时候，各种不可控因素会导致一个或多个区域出现计划外服务中断，对我们造成影响。 为了帮助你应对这些偶发事件，我们提供了下述针对 Azure 存储服务的概述性指导。

## <a name="how-to-prepare"></a>如何准备
每个客户都应准备好自己的灾难恢复计划，这很重要。 从存储中断进行恢复时，通常需要操作人员和自动化过程的参与，目的是在正常运行状态下重新激活应用程序。 制定自己的灾难恢复计划时，请参阅以下 Azure 文档：

* [可用性清单](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [设计适用于 Azure 的弹性应用程序](/azure/architecture/resiliency)
* [Azure Site Recovery 服务](https://azure.microsoft.com/services/site-recovery/)
* [Azure 存储复制](storage-redundancy)
* [Azure 备份服务](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>如何检测
若要确定 Azure 服务状态，建议订阅 [Azure 服务运行状况仪表板](https://azure.microsoft.com/status/)。

## <a name="what-to-do-if-a-storage-outage-occurs"></a>在存储空间中断时该怎么办
如果一个或多个区域的一个或多个存储服务临时不可用，可以考虑两种选项。 如果需要立即访问数据，请考虑“选项 2”。

### <a name="option-1-wait-for-recovery"></a>选项 1：等待恢复
在此情况下，不需要采取任何操作。 我们正在努力还原 Azure 服务的可用性。 可在 [Azure 服务运行状况仪表板](https://azure.microsoft.com/status/)上监视服务状态。

### <a name="option-2-copy-data-from-secondary"></a>选项 2：从辅助数据库复制数据
如果为存储帐户选择[读取访问异地冗余存储 (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage)（推荐），就可以从次要区域访问数据。 可使用 [AzCopy](storage-use-azcopy.md)、[Azure PowerShell](storage-powershell-guide-full.md) 和 [Azure 数据移动库](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)之类的工具将数据从次要区域复制到不受影响区域的其他存储帐户中，然后将应用程序指向该存储帐户，确保可读取和写入。

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>进行存储空间故障转移时会发生什么情况
如果选择[异地冗余存储 (GRS)](storage-redundancy.md#geo-redundant-storage) 或[读取访问地域冗余存储 (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage)（推荐），Azure 存储会将数据持久保存在两个区域（主要区域和次要区域）中。 在这两个区域中，Azure 存储始终维护数据的多个副本。

当区域灾难影响主要区域时，我们会首先尝试还原该区域的服务。 有时可能无法还原主要区域（情况极少），具体取决于灾难的性质及其影响。 在那种情况下，我们会进行异地故障转移。 跨区域数据复制是一个可能有延迟的异步过程，因此，可能会丢失尚未复制到次要区域的更改。 若要详细了解复制状态，可查阅[存储帐户的“上次同步时间”](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)。

有关存储空间异地故障转移体验的一些观点：

* 只能通过 Azure 存储团队触发存储空间异地故障转移 – 不需客户操作。
* 现存的 blob、表格、队列和文件的存储服务终结点在故障转移后将保持不变；Microsoft 提供的 DNS 入口将需要更新，以从主要区域切换到次要区域。  Microsoft 将作为异地故障转移进程的一部分自动执行此更新。
* 在异地故障转移之前和过程中，由于灾难的影响，将无法对存储帐户进行写入访问，但如果存储帐户已配置为 RA-GRS，仍然可以从次要区域进行读取。
* 完成异地故障转移且传播 DNS 更改后，将恢复对存储帐户的读取和写入访问权限；这会指向之前的辅助终结点。 
* 请注意，如果存储帐户配置了 GRS 或 RA-GRS，则将具有写入访问权限。 
* 若要了解更多详细信息，可查阅[存储帐户的“上次异地故障转移时间”](https://msdn.microsoft.com/library/azure/ee460802.aspx)。
* 在故障转移之后，存储帐户完全可以正常使用，但处于“已降级”状态，因为实际上它是托管在独立区域中，不可能进行异地复制。 为了缓解此风险，我们需要还原原始的主要区域，并通过异地故障回复还原原始状态。 如果原始的主要区域不可恢复，我们会分配其他次要区域。
  若要更详细地了解 Azure 存储异地复制的基础结构，请参阅存储团队博客中有关[冗余选项和 RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) 的文章。

## <a name="best-practices-for-protecting-your-data"></a>数据保护最佳实践
可以通过一些推荐的方法定期备份存储数据。

* VM 磁盘 – 利用 [Azure 备份服务](https://azure.microsoft.com/services/backup/)备份 Azure虚拟机所用的 VM 磁盘。
* 块 Blob – 使用 [AzCopy](storage-use-azcopy.md)、[Azure PowerShell](storage-powershell-guide-full.md) 或 [Azure 数据移动库](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)创建每个块 Blob 的[快照](https://msdn.microsoft.com/library/azure/hh488361.aspx)，或者将 Blob 复制到其他区域的其他存储帐户中。
* 表 – 使用 [AzCopy](storage-use-azcopy.md) 将表数据导出到其他区域的其他存储帐户中。
* 文件 – 使用 [AzCopy](storage-use-azcopy.md) 或 [Azure PowerShell](storage-powershell-guide-full.md) 将文件复制到其他区域的其他存储帐户。

若要深入了解如何创建充分利用 RA-GRS 功能的应用程序，请参阅[使用 RA-GRS 存储设计高可用性应用程序](../storage-designing-ha-apps-with-ragrs.md)

