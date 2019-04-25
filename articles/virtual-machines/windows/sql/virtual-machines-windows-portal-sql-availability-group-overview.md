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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325795"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>介绍 Azure 虚拟机上的 SQL Server Always On 可用性组 #

本文介绍 Azure 虚拟机上的 SQL Server 可用性组。 

Azure 虚拟机上的 Always On 可用性组类似于本地的 Always On 可用性组。 有关详细信息，请参阅 [Always On 可用性组 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

此关系图说明了 Azure 虚拟机中完整 SQL Server 可用性组的部件。

![可用性组](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure 虚拟机中可用性组的主要区别是 Azure 虚拟机需要[负载均衡器](../../../load-balancer/load-balancer-overview.md)。 负载均衡器保留可用性组侦听器的 IP 地址。 如果有多个可用性组，则每个组都需要一个侦听器。 一个负载均衡器可以支持多个侦听器。

此外，在 Azure IaaS VM 来宾故障转移群集上，我们建议每个服务器（群集节点）使用一个 NIC 和一个子网。 Azure 网络具有物理冗余，这使得在 Azure IaaS VM 来宾群集上不需要额外的 NIC 和子网。 虽然群集验证报告将发出警告，指出节点只能在单个网络上访问，但在 Azure IaaS VM 来宾故障转移群集上可以安全地忽略此警告。 

|  | Windows Server 版本 | SQL Server 版本 | SQL Server 版本 | WSFC 仲裁配置 | 使用多区域进行灾难恢复 | 多子网支持 | 支持现有 AD | 使用具有多个区域的相同区域进行灾难恢复 | Dist-AG 支持，没有 AD 域 | Dist-AG 支持，没有群集 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | 云见证 | 否 | 是 | 是 | 是 | 否 | 否 |
| [快速入门模板](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | 云见证 | 否 | 是 | 是 | 是 | 否 | 否 |
| [门户模板](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | 文件共享 | 否 | 否 | 否 | 否 | 否 | 否 |
| [手动](virtual-machines-windows-portal-sql-availability-group-prereq.md) | 全部 | 全部 | 全部 | 全部 | 是 | 是 | 是 | 是 | 是 | 是 |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

准备好在 Azure 虚拟机上生成 SQL Server 可用性组时，请参阅这些教程。

## <a name="manually-with-azure-cli"></a>使用 Azure CLI 手动
使用 Azure CLI 配置和部署某一可用性组是推荐的选项，因为它是在简单性和部署速度方面的最佳。 使用 Azure CLI、 将 SQL Server Vm 加入到群集中，在 Windows 故障转移群集的创建，以及创建侦听器和内部负载均衡器可以实现在 30 分钟内。 此选项仍需要手动创建可用性组，但可自动执行所有其他必需的配置步骤。 

有关详细信息，请参阅[使用 Azure SQL VM CLI 为 Azure VM 上的 SQL Server 中配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-cli.md)。 

## <a name="automatically-with-azure-quickstart-templates"></a>自动使用 Azure 快速入门模板
Azure 快速入门模板利用 SQL 虚拟机资源提供程序来部署 Windows 故障转移群集、 SQL Server Vm 加入该、 创建侦听器和配置内部负载均衡器。 此选项仍需要手动创建可用性组和内部负载均衡器 (ILB)，但可自动执行并简化了所有其他必需的配置步骤 （包括配置 ILB）。 

有关详细信息，请参阅[使用 Azure 快速入门模板为 Azure VM 上的 SQL Server 中配置 Always On 可用性组](virtual-machines-windows-sql-availability-group-quickstart-template.md)。


## <a name="automatically-with-an-azure-portal-template"></a>自动使用 Azure 门户模板

[在 Azure VM 中自动配置 Always On 可用性组 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>在 Azure 门户中手动操作

还可以不使用模板自行创建虚拟机。 首先完成先决条件，然后创建可用性组。 请参阅以下主题： 

- [配置 Azure 虚拟机上的 SQL Server Always On 可用性组的先决条件](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [创建 Always On 可用性组以提高可用性和灾难恢复能力](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>后续步骤

[在位于不同区域的 Azure 虚拟机上配置 SQL Server AlwaysOn 可用性组](virtual-machines-windows-portal-sql-availability-group-dr.md)
