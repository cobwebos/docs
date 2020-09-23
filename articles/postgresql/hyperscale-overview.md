---
title: Azure Database for PostgreSQL 超大规模 (Citus) 概述
description: 提供超大规模 (Citus) 部署选项的概述
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: e62d1bf0e9db5e80193cb0615d0a9d31e3041d63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943453"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>什么是 Azure Database for PostgreSQL 超大规模 (Citus)？

Azure Database for PostgreSQL 是 Microsoft 云中为开发人员构建的关系型数据库服务。 它基于开放源代码 [PostgreSQL](https://www.postgresql.org/) 数据库引擎的社区版本。

超大规模 (Citus) 是一种部署选项，可以使用分片跨多台计算机来水平缩放查询。 其查询引擎会将这些服务器的传入 SQL 查询并行化，加快大型数据集上的响应。 它为需要比其他部署选项更大规模和更高性能的应用程序提供服务：通常，工作负载接近或已经超过 100 GB 的数据。

超大规模 (Citus) 提供：

- 使用分片跨多个计算机进行水平缩放
- 将这些服务器的查询并行化，加快大型数据集上的响应
- 为多租户应用程序、实时运营分析以及高吞吐量事务工作负载提供卓越的支持

针对 PostgreSQL 构建的应用程序可以在超大规模 (Citus) 部署的基础上运行分布式查询，使用标准的[连接库](./concepts-connection-libraries.md)，基本不需要什么更改。

## <a name="next-steps"></a>后续步骤

- 请从[创建第一个](./quickstart-create-hyperscale-portal.md) Azure Database for PostgreSQL 超大规模 (Citus) 服务器组开始。
- 有关成本比较和计算器，请参阅[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。 超大规模 (Citus) 也提供预付费的预留实例折扣；有关详细信息，请参阅[超大规模 RI 定价](concepts-hyperscale-reserved-pricing.md)页。
- 确定服务器组的最佳[初始大小](howto-hyperscale-scaling.md#picking-initial-size)
