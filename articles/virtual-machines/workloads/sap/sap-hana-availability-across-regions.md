---
title: 跨 Azure 区域的 SAP HANA 可用性 | Microsoft Docs
description: 概述在多个 Azure 区域中的 Azure VM 上运行 SAP HANA 时的可用性注意事项。
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
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95ada2cb146bdbc972afee883a1d174c95aa67d7
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297576"
---
# <a name="sap-hana-availability-across-azure-regions"></a>跨 Azure 区域的 SAP HANA 可用性

本文介绍跨不同 Azure 区域的 SAP HANA 可用性的相关场景。 由于 Azure 区域之间的距离较远，在多个 Azure 区域中设置 SAP HANA 可用性时涉及到一些特殊的注意事项。

## <a name="why-deploy-across-multiple-azure-regions"></a>跨多个 Azure 区域部署的原因

不同 Azure 区域的相隔距离较远。 根据地缘政治区域，Azure 区域之间的距离可能有几百甚至几千英里，例如在美国。 由于这种距离，部署在两个不同 Azure 区域的资产之间的网络流量会发生明显的网络往返延迟的情况。 延迟的程度足以影响到典型 SAP 工作负载下，两个 SAP HANA 实例之间的同步数据交换。 

另一方面，组织通常对主要数据中心和辅助数据中心位置之间的距离有要求。 在较广泛的地理位置发生自然灾害时，距离要求有助于提供可用性。 例如，2017 年九月和十月在加勒比海和佛罗里达州发生的飓风。 组织可能至少有一个最短距离要求。 对于大多数 Azure 客户而言，此最短距离定义要求在设计时考虑跨 [Azure 区域](https://azure.microsoft.com/regions/)的可用性。 因为，如果两个 Azure 区域之间的距离过大，则不能使用 HANA 同步复制模式，RTO 和 RPO 的要求可能强迫你在一个区域内部署可用性配置，然后在第二个区域补充额外部署。

在这种情况下，要考虑的另一方面是故障转移和客户端重定向。 假设两个不同 Azure 区域中 SAP HANA 实例之间的故障转移始终是手动故障转移。 由于 SAP HANA 系统复制的复制模式设置为异步，因此可能出现主要 HANA 实例中提交的数据未到达次要 HANA 实例的情况。 因此，对于进行异步复制的配置，自动故障转移不能用作一个选项。 即使对于手动控制的故障转移（例如在故障转移练习中），仍需要采取措施确保所有在主要端提交的数据在手动转移到其他 Azure 区域之前，已到达辅助实例。
 
Azure 虚拟网络使用不同的 IP 地址范围。 IP 地址部署在第二个 Azure 区域。 因此，需要更改 SAP HANA 客户端配置，或者需要创建步骤来更改名称解析（首选）。 这样，客户端就会重定向到新辅助站点的服务器 IP 地址。 有关详细信息，请参阅 SAP 文章[接管后的客户端连接恢复](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)。   

## <a name="simple-availability-between-two-azure-regions"></a>两个 Azure 区域之间的简单可用性

可以选择不将任何可用性配置放在单个区域中，但如果发生灾难，仍需要为工作负荷提供服务。 这种情况的典型案例是非生产系统。 虽然可以保持系统关闭半日或整日，但不能允许系统在 48 小时或更长时间不可用。 为降低安装成本，可在重要性较低的 VM 中运行另一个系统。 另一个系统充当目标。 也可以将次要区域中的 VM 大小调小，并选择不预加载数据。 由于需要手动进行故障转移，并且还需要更多的步骤来完成整个应用程序堆栈的故障转移，因此可接受花费额外时间关闭 VM、重设其大小和重启 VM。

如果使用将 DR 目标与一个 VM 中的 QA 系统共享的方案，则需要考虑以下这些注意事项：

- 有两种[操作模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)（delta_datashipping 和 logreplay）可用于这类方案
- 这两种操作模式在不预加载数据的情况下具有不同的内存要求
- 在没有预加载选项的情况下，Delta_datashipping 可能需要的内存显著少于 logreplay。 请参阅 SAP 文档[如何为 SAP HANA 执行系统复制的](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)的第 4.3 章
- Logreplay 操作模式在不进行预加载情况下的内存要求不确定，取决于加载的列存储结构。 在极端情况下，可能需要主实例内存的 50%。 用于 logreplay 操作模式的内存与是否选择设置预加载数据无关。


![跨两个区域的两个 VM 示意图](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> 在此配置中，因为 HANA 系统复制模式为异步，所以不能提供 RPO=0。 如果需要提供 RPO=0，则不能选择该配置。

可以在配置中所做的轻微更改可能是将数据配置为预加载。 但是由于故障转移的手动性而且应用层需要移动到次要区域，预加载数据可能没有意义。 

## <a name="combine-availability-within-one-region-and-across-regions"></a>在一个区域内或跨区域合并可用性 

在一个区域内或跨区域进行可用性合并可由以下因素驱动：

- Azure 区域内对 RPO=0 的要求。
- 组织不希望或不能让全球运营受到波及较大范围的大型自然灾害影响。 例如，过去几年影响加勒比海的几场飓风。
- 规定要求的主要和辅助站点之间的距离明显超过 Azure 可用性区域可提供的距离。

在这种情况下，可以使用 HANA 系统复制设置 SAP 称为 [SAP HANA 多层系统复制配置](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)的配置。 体系结构如下所示：

![跨两个区域的三个 VM 示意图](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP 使用 HANA 2.0 SPS3 引入了[多目标系统复制](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html)。 多目标系统复制在更新方案中带来一些优势。 例如，当辅助 HA 站点关闭以进行维护或更新时，DR 站点（区域 2）不会受到影响。 可以在[此处](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html)找到有关 HANA 多目标系统复制的更多信息。
使用多目标复制的可能体系结构如下所示：

![三个 VM 在两个区域上的多目标图](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

如果组织对第二个 (DR) Azure 区域的高可用性就绪性有要求，那么体系结构将如下所示：

![三个 VM 在两个区域上的多目标图](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


使用 logreplay 作为操作模式时，此配置在主要区域内提供低 RTO 的 RPO=0。 如果需要转移到第二个区域，此配置还能提供更低的 RPO。 第二个区域的 RTO 时间取决于是否预加载数据。 许多客户使用次要区域中的 VM 来运行测试系统。 在这种用例中，无法预加载数据。

> [!IMPORTANT]
> 不同层之间的操作模式需要是同类的。 不能使用 logreply 作为第 1 层与第 2 层之间的操作模式，同时使用 delta_datashipping 提供第 3 层。 只能选择一种或另一种操作模式，需要对所有层保持一致。 由于 delta_datashipping 不适合提供 RPO=0，因此对于这类多层配置，唯一合理的操作模式仍是 logreplay。 有关操作模式和某些限制的详细信息，请参阅 SAP 文章 [SAP HANA 系统复制的操作模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)。 

## <a name="next-steps"></a>后续步骤

有关在 Azure 中设置这些配置的分步指导，请参阅：

- [在 Azure VM 中设置 SAP HANA 系统复制](sap-hana-high-availability.md)
- [使用系统复制实现 SAP HANA 的高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
