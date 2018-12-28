---
title: Azure SQL 数据库托管实例确定 VNet/子网大小 | Microsoft Docs
description: 本主题介绍如何计算将在其中部署 Azure SQL 数据库托管实例的子网大小。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: a588f8579d45f6230c80a62f8cd3abc155d75700
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344963"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>根据 Azure SQL 数据库托管实例确定 VNet子网大小

Azure SQL 数据库托管实例必须部署在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中。

可以在 VNet 子网中部署的托管实例数取决于子网的大小（子网范围）。

创建托管实例时，Azure 会根据预配期间选择的层，分配一定数量的虚拟机。 由于这些虚拟机与子网关联，因此它们需要 IP 地址。 为了确保日常操作和服务维护期间保持高可用性，Azure 可能会分配更多的虚拟机。 因此，子网中所需的 IP 地址数大于该子网中的托管实例数。 

根据设计，托管实例至少需要子网中的 16 个 IP 地址，有时使用多达 256 个 IP 地址。 因此，在定义子网 IP 范围时，可以使用子网掩码 /28 到 /24。 

> [!IMPORTANT]
> 具有 16 个 IP 地址的子网大小是绝对最小值，进一步实现托管实例横向扩展的潜力有限。强烈建议选择前缀为 /27 或更低值的子网。 

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
- 
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)
