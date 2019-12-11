---
title: 节点–超大规模（Citus）-Azure Database for PostgreSQL
description: 在 Azure Database for PostgreSQL 的服务器组中了解两种类型的节点：协调器和辅助角色。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973996"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 中的节点–超大规模（Citus）

超大规模（Citus）宿主类型允许 Azure Database for PostgreSQL 服务器（称为节点）在 "无共享" 结构中彼此协调。 服务器组中的节点共同保存更多的数据，并使用比单个服务器上的更多的 CPU 内核。 该体系结构还允许通过向服务器组中添加更多节点来缩放数据库。

## <a name="coordinator-and-workers"></a>协调器和辅助角色

每个服务器组都有一个协调器节点和多个辅助角色。 应用程序将其查询发送到协调器节点，该节点将其中继到相关的工作线程并累计其结果。 应用程序无法直接连接到辅助角色。

对于每个查询，协调器会将其路由到单个辅助角色节点，或将其并行在多个节点上，具体取决于所需的数据是在单个节点上还是在多个节点上。 协调器通过咨询元数据表确定要执行的操作。 这些表跟踪辅助角色节点的 DNS 名称和运行状况，以及跨节点分布的数据。

## <a name="next-steps"></a>后续步骤
- 了解节点如何存储[分布式数据](concepts-hyperscale-distributed-data.md)
