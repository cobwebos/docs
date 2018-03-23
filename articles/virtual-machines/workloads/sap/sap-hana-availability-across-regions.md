---
title: 跨 Azure 区域的 SAP HANA 可用性 | Microsoft Docs
description: 介绍在 Azure VM 上运行 SPA HANA 时的可用性注意事项的概述
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>跨 Azure 区域的 SAP HANA 可用性
本文介绍并探讨了跨不同 Azure 区域的 SAP HANA 可用性的方案。 考虑到分离的 Azure 区域之间距离较远，本文列出了需要特别注意的事项。

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>跨多个 Azure 区域部署的目的
不同 Azure 区域距离较远。 根据地缘政治区域，这个距离可能是几百甚至几千英里，例如在美国。 由于不同 Azure 区域之间的距离，部署在两个不同 Azure 区域的资产之间的网络流量会发生明显的网络往返延迟的情况。 这种延迟的程度足以影响到典型 SAP 工作负载下，两个 SAP HANA 实例之间的同步数据交换。 另一方面，还可能遇到以下情况：对于主要数据中心和次要数据中心之间的距离有明确要求，以在自然灾害影响较大区域范围时，确保可用性。 例如，2017 年九月和十月在加勒比海和佛罗里达州区域发生的飓风。 或者，至少有一个最小距离要求。 在大多数客户案例中，此最小距离定义都要求设计时考虑跨 [Azure 区域](https://azure.microsoft.com/regions/)的可用性。 因为，如果两个 Azure 区域之间的距离过大，则不能使用 HANA 的同步复制模式，RTO 和 RPO 的要求可能强迫你在一个区域内部署可用性配置，然后在第二个区域补充额外部署。

在这些配置中还需考虑的另一方面是故障转移和客户端重定向。 假设两个不同 Azure 区域中 SAP HANA 实例之间的故障转移始终是手动故障转移。 因为 SAP HANA 系统复制的复制模式设置为异步，因此可能出现主要 HANA 实例中提交的数据未到达次要 HANA 实例的情况。 因此，复制为异步的配置不能接受自动故障转移。 即使对于手动控制的故障转移（例如在故障转移练习中），仍需要采取措施确保所有在主要端提交的数据在手动转移到其他 Azure 区域之前，已到达辅助实例。
 
由于在 Azure VNet（在次要 Azure 区域中部署）中的不同 IP 地址范围操作，因此要么 SAP HANA 客户端需要在配置中进行更改，要么最好按步骤更改名称解析。 因此，客户端将重定向到新的辅助服务器 IP 地址。 若要了解详细信息，请参阅有关[接管后的客户端连接恢复](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)的 SAP 文章。   

## <a name="simple-availability-between-two-azure-regions"></a>两个 Azure 区域之间的简单可用性
此方案决定不将任何可用性配置放置在单个区域中。 但是如果发生灾难，需要为工作负载提供服务。 这种系统的典型案例是非生产系统。 虽然可以保持系统关闭半日或整日，也不允许系统在 48 小时或更长时间不可用。 为降低安装成本，可在充当目标的重要性较低的 VM 上运行其他系统，或者将次要区域中的 VM 大小设置为较小，并选择不预加载数据。 由于需要手动进行故障转移，并且还需要更多的步骤来完成整个应用程序堆栈的故障转移，因此可接受花费额外时间关闭 VM、重设其大小和再次启动 VM。

> [!NOTE]
> 即使不在 HANA 系统复制目标中预先加载数据，也至少需要 64 GB 内存，以便在目标实例的内存中保留行存储数据。

![跨两个区域的两个 VM](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> 在此配置中，因为 HANA 系统复制模式为异步，所以不能提供 RPO=0。 如果需要提供 RPO=0，则不能选择该配置。

对配置的小更改可以是配置数据预加载。 但是由于故障转移的手动性而且应用层需要移动到次要区域，预加载数据可能没有意义。 

## <a name="combine-availability-within-one-region-and-across-regions"></a>在一个区域内或跨区域合并可用性 
在一个区域内或跨区域进行可用性合并可由以下因素驱动：

- Azure 区域内对 RPO=0 的要求。
- 不希望或不能让公司的全球运营受到波及较大范围的大型自然灾害影响。 例如，过去几年影响加勒比海的几场飓风。
- 规定要求的主要和辅助站点之间的距离明显超过 Azure 可用性区域可提供的距离。

 
在这种情况下，可以使用 HANA 系统复制配置 SAP 称为 [SAP HANA 多层系统复制配置](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)的配置。 体系结构如下所示：

![跨两个区域的三个 VM](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

此配置在主要区域内提供具有少量 RTO 时间的 RPO=0，除此之外，还提供下降 RPO（如果移动到次要区域）。 次要区域的 RTO 时间取决于是否配置了数据预加载。 在许多客户案例中，次要区域中的 VM 用于运行测试系统。 因此，导致数据不能预加载。

> [!NOTE]
> 由于正在使用从第 1 层到第 2 层（主要区域中的同步复制）的 HANA 系统复制的 logreplay 操作模式，则第 2 层和第 3 层之间的复制（复制到次要站点）不能是 delta_datashipping 操作模式。 在[SAP HANA 系统复制的操作模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)中，SAP 记录了操作模式和部分限制条件的详细信息。 

## <a name="next-steps"></a>后续步骤
如需有关如何在 Azure 中设置此类配置的分步指导，请参阅以下文章：

- [在 Azure VM 中设置 SAP HANA 系统复制](sap-hana-high-availability.md)
- [Azure 上的 SAP – 第 4 部分 – 使用系统复制实现 SAP HANA 的高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
