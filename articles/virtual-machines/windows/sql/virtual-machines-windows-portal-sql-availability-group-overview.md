---
title: SQL Server 可用性组 - Azure 虚拟机 - 概述 | Microsoft Docs
description: 本文介绍 Azure 虚拟机上的 SQL Server 可用性组。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: d89e25b6a39649bb8421fd32b8842c3c76807268
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102162"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>介绍 Azure 虚拟机上的 SQL Server Always On 可用性组 #

本文介绍 Azure 虚拟机上的 SQL Server 可用性组。 

Azure 虚拟机上的 Always On 可用性组类似于本地的 Always On 可用性组。 有关详细信息，请参阅 [Always On 可用性组 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

此关系图说明了 Azure 虚拟机中完整 SQL Server 可用性组的部件。

![可用性组](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure 虚拟机中可用性组的主要区别是 Azure 虚拟机需要[负载均衡器](../../../load-balancer/load-balancer-overview.md)。 负载均衡器保留可用性组侦听器的 IP 地址。 如果有多个可用性组，则每个组都需要一个侦听器。 一个负载均衡器可以支持多个侦听器。

此外，在 Azure IaaS VM 来宾故障转移群集上，我们建议每个服务器（群集节点）使用一个 NIC 和一个子网。 Azure 网络具有物理冗余，这使得在 Azure IaaS VM 来宾群集上不需要额外的 NIC 和子网。 虽然群集验证报告将发出警告，指出节点只能在单个网络上访问，但在 Azure IaaS VM 来宾故障转移群集上可以安全地忽略此警告。 

若要提高冗余性和高可用性, SQL Server Vm 应位于同一[可用性集](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)或不同的[可用性区域](/azure/availability-zones/az-overview)中。 

|  | Windows Server 版本 | SQL Server 版本 | SQL Server 版本 | WSFC 仲裁配置 | 使用多区域进行灾难恢复 | 多子网支持 | 支持现有 AD | 使用具有多个区域的相同区域进行灾难恢复 | Dist-AG 支持，没有 AD 域 | Dist-AG 支持，没有群集 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | 云见证 | 否 | 是 | 是 | 是 | 否 | 否 |
| [快速入门模板](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | 云见证 | 否 | 是 | 是 | 是 | 否 | 否 |
| [门户模板](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | 文件共享 | 否 | 否 | 否 | 否 | 否 | 否 |
| [Manual](virtual-machines-windows-portal-sql-availability-group-prereq.md) | 全部 | 全部 | 全部 | 全部 | 是 | 是 | 是 | 是 | 是 | 是 |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

准备好在 Azure 虚拟机上生成 SQL Server 可用性组时，请参阅这些教程。

## <a name="manually-with-azure-cli"></a>手动与 Azure CLI
建议使用 Azure CLI 来配置和部署可用性组, 因为它是最简单的部署和部署速度。 在 Azure CLI 中, Windows 故障转移群集的创建、将 SQL Server Vm 加入到群集, 以及创建侦听器和内部负载均衡器都可以在30分钟内实现。 此选项仍需要手动创建可用性组, 但会自动执行所有其他必要的配置步骤。 

有关详细信息, 请参阅[使用 AZURE SQL VM CLI 在 AZURE VM 上配置 SQL Server Always On 可用性组](virtual-machines-windows-sql-availability-group-cli.md)。 

## <a name="automatically-with-azure-quickstart-templates"></a>自动通过 Azure 快速入门模板
Azure 快速入门模板利用 SQL VM 资源提供程序来部署 Windows 故障转移群集, 将 SQL Server Vm 加入到该群集, 创建侦听器, 并配置内部负载均衡器。 此选项仍需要手动创建可用性组和内部负载均衡器 (ILB), 但会自动执行并简化所有其他必要的配置步骤 (包括 ILB 的配置)。 

有关详细信息, 请参阅[使用 Azure 快速入门模板为 AZURE VM 上的 SQL Server 配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-quickstart-template.md)。


## <a name="automatically-with-an-azure-portal-template"></a>使用 Azure 门户模板自动

[在 Azure VM 中自动配置 Always On 可用性组 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>在 Azure 门户中手动操作

还可以不使用模板自行创建虚拟机。 首先完成先决条件，然后创建可用性组。 请参阅以下主题： 

- [配置 Azure 虚拟机上的 SQL Server Always On 可用性组的先决条件](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [创建 Always On 可用性组以提高可用性和灾难恢复能力](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>后续步骤

[在位于不同区域的 Azure 虚拟机上配置 SQL Server AlwaysOn 可用性组](virtual-machines-windows-portal-sql-availability-group-dr.md)
