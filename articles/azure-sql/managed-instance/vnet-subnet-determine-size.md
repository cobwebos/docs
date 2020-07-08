---
title: 确定所需的子网大小 & 范围
titleSuffix: Azure SQL Managed Instance
description: 本主题介绍如何计算将部署 Azure SQL 托管实例的子网的大小。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 562766ada8fb9a2620fa83875dc98d02ab752d95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85338554"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>确定 Azure SQL 托管实例 & 范围中所需的子网大小
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

必须在 Azure[虚拟网络（VNet）](../../virtual-network/virtual-networks-overview.md)中部署 azure SQL 托管实例。

可在 VNet 的子网中部署的托管实例数取决于子网的大小（子网范围）。

创建托管实例时，Azure 会根据预配期间选择的层分配多个虚拟机。 由于这些虚拟机与子网关联，因此它们需要 IP 地址。 为了确保日常操作和服务维护期间保持高可用性，Azure 可能会分配更多的虚拟机。 因此，子网中所需的 IP 地址数大于该子网中的托管实例数。

按照设计，托管实例在子网中需要至少32个 IP 地址。 因此，在定义子网 IP 范围时，可以使用最小的子网掩码/27。 建议你仔细规划托管实例部署的子网大小。 在规划过程中应考虑的输入包括：

- 包含以下实例参数的托管实例的数目：
  - 服务层
  - 硬件生成
  - Vcore 数
- 计划扩展/缩减或更改服务层

> [!IMPORTANT]
> 具有16个 IP 地址（子网掩码/28）的子网大小允许在其中部署托管实例，但它应仅用于部署用于评估的单一实例或在开发/测试方案中，不会执行缩放操作。

## <a name="determine-subnet-size"></a>确定子网大小

根据将来的实例部署和缩放需求调整子网的大小。 以下参数有助于构造计算：

- Azure 使用子网中的 5 个 IP 地址来满足自身的需要
- 每个虚拟群集分配额外数量的地址 
- 每个托管实例都使用依赖于定价层和硬件生成的地址数量

> [!IMPORTANT]
> 如果子网中存在任何资源，则不能更改子网地址范围。 也无法将托管实例从一个子网移到另一个子网。 请尽可能考虑使用较大的子网而不是较小的子网，以防止将来出现问题。

GP = 常规用途;BC = 业务关键;VC = 虚拟群集

| **硬件代** | **定价层** | **Azure 使用情况** | **VC 使用情况** | **实例使用情况** | **总*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \*列总数显示在子网中部署一个实例时要执行的地址数。 子网中的其他每个实例都会添加使用实例使用情况列表示的地址数。 使用 "Azure 使用情况" 列表示的地址在多个虚拟群集之间共享，而使用 "VC 使用情况" 列表示的地址在虚拟群集中放置的各个实例之间共享。

更新操作通常要求调整虚拟群集大小。 在某些情况下，更新操作将需要创建虚拟群集（有关详细信息，请参阅[管理操作一文](sql-managed-instance-paas-overview.md#management-operations)）。 在创建虚拟群集的情况下，所需的额外地址数等于 "VC 使用情况" 列所表示的地址数与放置在该虚拟群集中的实例所需的地址（"实例使用情况" 列）。

**示例 1**：计划有一个常规用途的托管实例（Gen4 硬件）和一个业务关键托管实例（Gen5 硬件）。 这意味着，你需要至少 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 个 IP 地址才能进行部署。 由于 IP 范围是用2的幂来定义的，因此子网需要此部署的最小 IP 范围为32（2 ^ 5）。<br><br>
如前所述，在某些情况下，更新操作将需要创建虚拟群集。 这意味着，如果对需要创建虚拟群集的 Gen5 业务关键托管实例进行更新，则需要提供额外的 6 + 5 = 11 个 IP 地址。 由于你已在使用32地址的22个，因此此操作没有可用的地址。 因此，需要保留子网掩码为/26 （64地址）的子网。

**示例 2**：计划在同一子网中使用三个常规用途（Gen5 硬件）和两个业务关键托管实例（Gen5 硬件）。 这意味着需要 5 + 6 + 3 * 3 + 2 * 5 = 30 个 IP 地址。 因此，你需要保留子网掩码为/26 的子网。 选择/27 的子网掩码会导致剩余的地址数为2（32–30），这会阻止所有实例的更新操作，因为子网中需要额外的地址来执行实例缩放。

**示例 3**：规划一个常规用途的托管实例（Gen5 硬件），并随时执行 vcore 更新操作。 这意味着需要 5 + 6 + 1 * 3 + 3 = 17 个 IP 地址。 由于 IP 范围定义为 2 的 N 次方，因此需要 32 个 (2^5) IP 地址。 因此，需要保留子网掩码为 /27 的子网。

### <a name="address-requirements-for-update-scenarios"></a>解决更新方案的要求

在缩放操作实例中，临时需要依赖于定价层和硬件生成的额外 IP 容量

| **硬件代** | **定价层** | **方案** | **其他地址*** |
| --- | --- | --- | --- |
| Gen4 | GP 或 BC | 缩放 Vcore | 5 |
| Gen4 | GP 或 BC | 缩放存储 | 5 |
| Gen4 | GP 或 BC | 从 GP 切换到 BC 或 BC 到 GP | 5 |
| Gen4 | GP | 切换到 Gen5 * | 9 |
| Gen4 | BC | 切换到 Gen5 * | 11 |
| Gen5 | GP | 缩放 Vcore | 3 |
| Gen5 | GP | 缩放存储 | 0 |
| Gen5 | GP | 切换到 BC | 5 |
| Gen5 | BC | 缩放 Vcore | 5 |
| Gen5 | BC | 缩放存储 | 5 |
| Gen5 | BC | 切换到 GP | 3 |

  \*Gen4 硬件正在逐步推出，在新部署中不再可用。 将硬件从 Gen4 更新为 Gen5，以利用特定于 Gen5 硬件生成的功能。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 AZURE SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 详细了解[SQL 托管实例的连接体系结构](connectivity-architecture-overview.md)。
- 请参阅如何[创建将在其中部署 SQL 托管实例的 VNet](virtual-network-subnet-create-arm-template.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](custom-dns-configure.md)。
