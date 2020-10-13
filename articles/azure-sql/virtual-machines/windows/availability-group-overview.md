---
title: SQL Server Always On 可用性组概述
description: 本文介绍 Azure 虚拟机上的 SQL Server Always On 可用性组。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 34d76d7c85a478b5e31a692e653752aa1653884c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293656"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure 虚拟机上的 SQL Server Always On 可用性组简介

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍 Azure 虚拟机上的 SQL Server 可用性组。 

Azure 虚拟机上的 AlwaysOn 可用性组类似于本地的 AlwaysOn 可用性组。 有关详细信息，请参阅 [Always On 可用性组 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

下图阐述了 Azure 虚拟机中完整 SQL Server 可用性组的各个部分。

![可用性组](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Azure 虚拟机中可用性组的主要区别是这些虚拟机 (VM) 需要[负载均衡器](../../../load-balancer/load-balancer-overview.md)。 负载均衡器保存可用性组侦听器的 IP 地址。 如果有多个可用性组，则每个组都需要一个侦听程序。 一个负载均衡器可以支持多个侦听器。

此外，在 Azure IaaS VM 来宾故障转移群集上，我们建议每个服务器（群集节点）使用一个 NIC 和一个子网。 Azure 网络具有物理冗余，这使得在 Azure IaaS VM 来宾群集上不需要额外的 NIC 和子网。 虽然群集验证报告将发出警告，指出节点只能在单个网络上访问，但在 Azure IaaS VM 来宾故障转移群集上可以安全地忽略此警告。 

要增加冗余和提高可用性，SQL Server VM 应位于相同的[可用性集](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets)或不同的[可用性区域](/azure/availability-zones/az-overview)中。 

|  | Windows Server 版本 | SQL Server 版本 | SQL Server 版本 | WSFC 仲裁配置 | 使用多区域进行灾难恢复 | 多子网支持 | 支持现有 AD | 使用具有多个区域的相同区域进行灾难恢复 | Dist-AG 支持，没有 AD 域 | Dist-AG 支持，没有群集 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[Azure 门户](availability-group-azure-portal-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | 企业版 | 云见证 | 否 | 是 | 是 | 是 | 否 | 否 |
| **[Azure CLI / PowerShell](availability-group-az-cli-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016   | 企业版 | 云见证 | 否 | 是 | 是 | 是 | 否 | 否 |
| **[快速启动模板](availability-group-quickstart-template-configure.md)** | 2019 </br> 2016 | 2019 </br>2017 </br>2016  | 企业版 | 云见证 | 否 | 是 | 是 | 是 | 否 | 否 |
| **[手动](availability-group-manually-configure-prerequisites-tutorial.md)** | 全部 | 全部 | 全部 | 全部 | 是 | 是 | 是 | 是 | 是 | 是 |

“SQL Server AlwaysOn 群集（预览版）”模板已从 Azure 市场中删除，并且不再提供。 

准备好在 Azure 虚拟机上生成 SQL Server 可用性组时，请参阅这些教程。

## <a name="manually-with-azure-cli"></a>使用 Azure CLI 手动生成

建议使用 Azure CLI 配置和部署可用性组，因为它是最简单、最快的部署方式。 使用 Azure CLI 中，创建 Windows 故障转移群集、将 SQL Server VM 加入群集以及创建侦听器和内部负载均衡器均可在 30 分钟内完成。 此选项仍然需要手动创建可用性组，但它会自动完成所有其他必要的配置步骤。 

有关详细信息，请参阅[使用 Azure SQL VM CLI 为 Azure VM 上的 SQL Server 配置 Always On 可用性组](availability-group-az-cli-configure.md)。 

## <a name="automatically-with-azure-quickstart-templates"></a>使用 Azure 快速启动模板自动生成

Azure 快速启动模板使用 SQL VM 资源提供程序来创建 Windows 故障转移群集、将 SQL Server VM 加入到该群集、创建侦听器以及配置内部负载均衡器。 此选项仍然需要手动创建可用性组和内部负载均衡器 (ILB)。 但是，它会自动执行并简化所有其他必要的配置步骤，包括 ILB 的配置。 

有关详细信息，请参阅[使用 Azure 快速启动模板为 Azure VM 上的 SQL Server 配置 Always On 可用性组](availability-group-quickstart-template-configure.md)。


## <a name="automatically-with-an-azure-portal-template"></a>使用 Azure 门户模板自动生成

[在 Azure VM 中自动配置 Always On 可用性组 - Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>在 Azure 门户中手动操作

还可以自行创建虚拟机，不需模板。 首先完成先决条件，然后创建可用性组。 请参阅下列主题： 

- [在 Azure 虚拟机上配置 SQL Server Always On 可用性组的先决条件](availability-group-manually-configure-prerequisites-tutorial.md)

- [创建 Always On 可用性组以提高可用性和灾难恢复能力](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>后续步骤

[在位于不同区域的 Azure 虚拟机上配置 SQL Server AlwaysOn 可用性组](availability-group-manually-configure-multiple-regions.md)
