---
title: 确定所需的子网大小和范围
titleSuffix: Azure SQL Managed Instance
description: 本主题介绍如何计算将在其中部署 Azure SQL 托管实例的子网的大小。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617632"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>确定 Azure SQL 托管实例所需的子网大小和范围
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例必须部署在 Azure [虚拟网络 (VNet)](../../virtual-network/virtual-networks-overview.md) 中。

可以在 VNet 子网中部署的托管实例数取决于子网的大小（子网范围）。

创建托管实例时，Azure 会根据预配期间选择的层，分配一定数量的虚拟机。 由于这些虚拟机与子网关联，因此它们需要 IP 地址。 为了确保日常操作和服务维护期间保持高可用性，Azure 可能会分配更多的虚拟机。 因此，子网中所需的 IP 地址数大于该子网中的托管实例数。

根据设计，一个托管实例在一个子网中至少需要 32 个 IP 地址。 因此，在定义子网 IP 范围时，可以使用最小的子网掩码，即 /27。 建议仔细规划用于部署托管实例的子网大小。 在规划过程中应纳入考虑的输入内容包括：

- 包含以下实例参数的托管实例的数量：
  - 服务层
  - 硬件代系
  - vCore 数目
- 计划纵向扩展/缩减或更改服务层

> [!IMPORTANT]
> 使用具有 16 个 IP 地址（子网掩码 /28）的子网即可部署托管实例，但应仅使用它来部署用于评估的或不执行缩放操作的开发/测试方案中的单个实例。

## <a name="determine-subnet-size"></a>确定子网大小

根据将来的实例部署和缩放需求调整子网大小。 可借助以下参数进行计算：

- Azure 使用子网中的 5 个 IP 地址来满足自身的需要
- 每个虚拟群集分配额外数量的地址 
- 每个托管实例使用一定数量的地址，具体取决于定价层和硬件代系

> [!IMPORTANT]
> 如果子网中存在任何资源，则无法更改子网地址范围。 也无法将托管实例从一个子网移动到另一个子网。 请尽量考虑使用更大的子网，而不使用较小的子网，以防止将来出现问题。

GP = 常规用途；BC = 业务关键；VC = 虚拟群集

| **硬件代系** | **定价层** | **Azure 使用情况** | **VC 使用情况** | **实例使用情况** | **总计*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \* 列总数显示在子网中部署一个实例时将采用的地址数。 子网中每增加一个实例，都会增加“实例使用情况”列表示的地址数。 “Azure 使用情况”列表示的地址在多个虚拟群集之间共享，而“VC 使用情况”列表示的地址在该虚拟群集中放置的实例之间共享。

执行更新操作时，通常需要调整虚拟群集的大小。 在某些情况下，执行更新操作需要创建虚拟群集（有关更多详细信息，请参阅[管理操作文章](sql-managed-instance-paas-overview.md#management-operations)）。 如果要创建虚拟群集，所需的额外地址数等于“VC 使用情况”列表示的地址数加上该虚拟群集中放置的实例所需的地址数（“实例使用情况”列）。

**示例 1**：你计划使用一个常规用途托管实例（Gen4 硬件）和一个业务关键托管实例（Gen5 硬件）。 这意味着，至少需要 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 个 IP 地址才能完成部署。 由于 IP 范围定义为 2 的 N 次方，因此子网要求该部署的最小 IP 范围为 32 (2^5) 个地址。<br><br>
如上所述，在某些情况下，执行更新操作需要创建虚拟群集。 这意味着，这里举个例子，如果要更新到需要创建虚拟群集的 Gen5 业务关键托管实例，就需要额外的 6 + 5 = 11 个可用 IP 地址。 由于已经使用了 22 个地址（共 32 个地址），没有足够的可用地址可用来实现此操作。 因此，需要保留子网掩码为 /26（64 个地址）的子网。

**示例 2**：你计划将三个常规用途（Gen5 硬件）和两个业务关键托管实例（Gen5 硬件）置于同一子网中。 这意味着，你需要 5 + 6 + 3 * 3 + 2 * 5 = 30 个 IP 地址。 因此，你需要保留子网掩码为 /26 的子网。 选择/27 的子网掩码会导致剩余的地址数为 2 (32 – 30) ，这会阻止所有实例的更新操作，因为子网中需要额外的地址来执行实例缩放。

**示例 3**：你计划使用一个常规用途托管实例（Gen5 硬件），并不时执行 vCores 更新操作。 这意味着，你需要 5 + 6 + 1 * 3 + 3 = 17 个 IP 地址。 由于 IP 范围定义为 2 的 N 次方，因此需要 32 个 (2^5) IP 地址。 因此，需要保留子网掩码为 /27 的子网。

### <a name="address-requirements-for-update-scenarios"></a>“更新”方案的地址要求

在缩放期间，实例临时需要额外的 IP 容量，该容量具体取决于定价层和硬件代系

| **硬件代系** | **定价层** | **方案** | **更多地址*** |
| --- | --- | --- | --- |
| Gen4 | GP 或 BC | 缩放 Vcore 数目 | 5 |
| Gen4 | GP 或 BC | 缩放存储 | 5 |
| Gen4 | GP 或 BC | 从 GP 切换到 BC 或从 BC 切换到 GP | 5 |
| Gen4 | GP | 正在切换到 Gen5* | 9 |
| Gen4 | BC | 正在切换到 Gen5* | 11 |
| Gen5 | GP | 缩放 Vcore 数目 | 3 |
| Gen5 | GP | 缩放存储 | 0 |
| Gen5 | GP | 正在切换到 BC | 5 |
| Gen5 | BC | 缩放 Vcore 数目 | 5 |
| Gen5 | BC | 缩放存储 | 5 |
| Gen5 | BC | 正在切换到 GP | 3 |

  \* Gen4 硬件正在逐步被淘汰，不能再用于新部署。 将硬件代系从 Gen4 更新为 Gen5，以利用特定于 Gen5 硬件代系的功能。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 详细了解 [SQL 托管实例中的连接体系结构](connectivity-architecture-overview.md)。
- 请参阅并了解如何[创建将在其中部署 SQL 托管实例的 VNet](virtual-network-subnet-create-arm-template.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](custom-dns-configure.md)。
