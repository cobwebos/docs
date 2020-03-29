---
title: 节点 = 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 在 PostgreSQL Azure 数据库中的服务器组中了解两种类型的节点（协调器和辅助角色）。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973996"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 数据库中的节点，用于后格雷SQL和超大规模（Citus）

Hyperscale （Citus） 托管类型允许用于 PostgreSQL 服务器（称为节点）的 Azure 数据库在"共享无"体系结构中相互协调。 服务器组中的节点共同保存的数据更多，并且使用的 CPU 内核比在单个服务器上可能拥有的数据更多。 该体系结构还允许数据库通过向服务器组添加更多节点来扩展。

## <a name="coordinator-and-workers"></a>协调员和工人

每个服务器组都有一个协调器节点和多个辅助工作群体。 应用程序将其查询发送到协调器节点，该节点将其中继到相关工作人员并累积其结果。 应用程序无法直接连接到工作人员。

对于每个查询，协调器要么将其路由到单个辅助节点，要么将其并行化到多个节点，具体取决于所需的数据是驻在单个节点还是多个节点上。 协调员通过咨询元数据表来决定该怎么做。 这些表跟踪辅助节点的 DNS 名称和运行状况，以及数据在节点之间的分布。

## <a name="next-steps"></a>后续步骤
- 了解节点如何存储[分布式数据](concepts-hyperscale-distributed-data.md)
