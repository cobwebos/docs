---
title: SQL Server 可用性组 - Azure 虚拟机 - 概述 | Microsoft Docs
description: 本文介绍 Azure 虚拟机上的 SQL Server 可用性组。
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.openlocfilehash: 4a531fb87d9cd2743138ba7a027bdf0d132b9747
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29396425"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>介绍 Azure 虚拟机上的 SQL Server Always On 可用性组 #

本文介绍 Azure 虚拟机上的 SQL Server 可用性组。 

Azure 虚拟机上的 Always On 可用性组类似于本地的 Always On 可用性组。 有关详细信息，请参阅 [Always On 可用性组 (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx)。 

此关系图说明了 Azure 虚拟机中完整 SQL Server 可用性组的部件。

![可用性组](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure 虚拟机中可用性组的主要区别是 Azure 虚拟机需要[负载均衡器](../../../load-balancer/load-balancer-overview.md)。 负载均衡器保留可用性组侦听器的 IP 地址。 如果有多个可用性组，则每个组都需要一个侦听器。 一个负载均衡器可以支持多个侦听器。

准备好在 Azure 虚拟机上生成 SQL Server 可用性组时，请参阅这些教程。

## <a name="automatically-create-an-availability-group-from-a-template"></a>从模板自动创建可用性组

[在 Azure VM 中自动配置 Always On 可用性组 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>在 Azure 门户中手动创建可用性组

还可以不使用模板自行创建虚拟机。 首先完成先决条件，然后创建可用性组。 请参阅以下主题： 

- [配置 Azure 虚拟机上的 SQL Server Always On 可用性组的先决条件](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [创建 Always On 可用性组以提高可用性和灾难恢复能力](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>后续步骤

[在不同区域中的 Azure 虚拟机上配置 SQL Server Always On 可用性组](virtual-machines-windows-portal-sql-availability-group-dr.md)。
