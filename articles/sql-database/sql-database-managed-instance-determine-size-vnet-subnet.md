---
title: Azure SQL 数据库托管实例确定 VNet/子网大小
description: 本主题介绍如何计算将在其中部署 Azure SQL 数据库托管实例的子网大小。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: fb0d79a82553349d5dad547a2d78a460301cbd6d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688112"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>根据 Azure SQL 数据库托管实例确定 VNet子网大小

Azure SQL 数据库托管实例必须部署在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中。

可以在 VNet 子网中部署的托管实例数取决于子网的大小（子网范围）。

创建托管实例时，Azure 会根据预配期间选择的层，分配一定数量的虚拟机。 由于这些虚拟机与子网关联，因此它们需要 IP 地址。 为了确保日常操作和服务维护期间保持高可用性，Azure 可能会分配更多的虚拟机。 因此，子网中所需的 IP 地址数大于该子网中的托管实例数。

根据设计，托管实例至少需要子网中的 16 个 IP 地址，有时使用多达 256 个 IP 地址。 因此，在定义子网 IP 范围时，可以使用介于 /28 和 /24 之间的子网掩码。 对于单一的常规用途或业务关键部署，网络掩码位 /28（每个网络 14 台主机）是一个不错的大小。 对于位于同一 VNet 内的多个托管实例部署，掩码位 /27（每个网络 30 台主机）是理想的。 掩码位设置 /26（62 台主机）和 /24（254 台主机）允许进一步横向扩展 VNet 来支持更多的托管实例。

> [!IMPORTANT]
> 具有16个 IP 地址的子网大小是不受限制的，其中不支持 vCore 大小变化之类的缩放操作。 强烈建议选择带有前缀/27 或最长前缀的子网。

## <a name="determine-subnet-size"></a>确定子网大小

如果打算在子网中部署多个托管实例并需要优化子网大小，可使用以下参数来完成计算：

- Azure 使用子网中的 5 个 IP 地址来满足自身的需要
- 每个常规用途实例需要 2 个地址
- 每个业务关键实例需要四个地址

**示例**：你计划拥有三个常规用途和两个业务关键托管实例。 这意味着，需要 5 + 3 * 2 + 2 * 4 = 19 个 IP 地址。 由于 IP 范围定义为 2 的 N 次方，因此需要 32 个 (2^5) IP 地址。 因此，需要保留子网掩码为 /27 的子网。

> [!IMPORTANT]
> 上方显示的计算将随着进一步改进而变得过时。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)。
- 详细了解[托管实例中的连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
- 请参阅[如何创建将在其中部署托管实例的 VNet](sql-database-managed-instance-create-vnet-subnet.md)
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)
