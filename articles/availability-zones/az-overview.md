---
title: Azure 可用性区域概述 | Microsoft Docs
description: 本文概述如何使用可用性区域在 Azure 中创建高度可用的弹性应用程序
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: a4133779538e412a19a11de678b1527fb8023a87
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="overview-of-availability-zones-in-azure"></a>Azure 中的可用性区域概述
可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 区域中可用性区域的物理隔离可以在发生数据中心故障的情况下保护应用程序和数据。 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。 Azure 凭借可用性区域提供一流的 99.99% VM 运行时间 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

通过将计算、存储、网络和数据资源共置在一个区域并将其复制到其他区域，在应用程序体系结构中内置高可用性。 支持可用性区域的 Azure 服务划分为两类：

- **区域服务** – 将资源（例如虚拟机、托管磁盘和 IP 地址）固定到特定的区域，或
- **区域冗余服务** – 平台自动跨区域复制（例如区域冗余存储和 SQL 数据库）。

若要在 Azure 上实现全面的业务连续性，可结合使用可用性区域和 Azure 区域对来构建应用程序体系结构。 可以使用 Azure 区域中的可用性区域以同步方式复制应用程序和数据以实现高可用性，并以异步方式跨 Azure 区域复制以实现灾难恢复保护。
 
![地区中发生故障的一个区域的概念视图](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>支持可用性区域的地区

- 美国中部
- 法国中部
- 美国东部 2（预览版）
- 西欧（预览版）
- 东南亚（预览版）


## <a name="services-that-support-availability-zones"></a>支持可用性区域的服务
支持可用性区域的 Azure 服务有：

- Linux 虚拟机
- Windows 虚拟机
- 虚拟机规模集
- 托管磁盘
- 负载均衡器
- 公共 IP 地址
- 区域冗余存储
- SQL 数据库


## <a name="pricing"></a>定价
在可用性区域中部署虚拟机不会产生额外的费用。 跨一个 Azure 区域中的两个或更多个可用性区域部署两个或更多个虚拟机后，可获得 99.99% VM 运行时间 SLA。 会产生可用性区域间 VM 到 VM 的数据传输费用。 有关详细信息，请查看[带宽定价](https://azure.microsoft.com/pricing/details/bandwidth/)页。


## <a name="get-started-with-availability-zones"></a>可用性区域入门
- [创建虚拟机](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 添加托管磁盘](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [创建区域冗余的虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有区域冗余前端的标准负载均衡器跨区域对 VM 进行负载均衡](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [使用具有区域性前端的标准负载均衡器在区域内对 VM 进行负载均衡](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [区域冗余存储](../storage/common/storage-redundancy-zrs.md)
- [SQL 数据库](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)


## <a name="next-steps"></a>后续步骤
- [快速入门模板](http://aka.ms/azqs)
