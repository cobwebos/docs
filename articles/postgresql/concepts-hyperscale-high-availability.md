---
title: 高可用性–超大规模（Citus）-Azure Database for PostgreSQL
description: 高可用性和灾难恢复的概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975527"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 中的高可用性–超大规模（Citus）

高可用性 (HA) 通过维护服务器组中每个节点的备用副本，避免数据库故障。 如果节点发生故障，超大规模会将传入连接从故障节点切换到备用节点。 故障转移会在几分钟内发生，并且通过 PostgreSQL 同步流复制，提升的节点始终可获取最新数据。

若要在协调器节点上利用 HA，数据库应用程序需要检测和重试已删除的连接和失败的事务。 可以通过相同的连接字符串来访问新提升的协调器。

恢复可以分为三个阶段： "检测"、"故障转移" 和 "完全恢复"。  超大规模在每个节点上运行定期运行状况检查，四次失败检查后，它会确定节点是否关闭。 然后，超大规模将备用节点升级为主节点状态（故障转移），并预配新的待机状态。
流式复制开始，使新节点保持最新状态。  复制所有数据后，节点已达到完全恢复。

### <a name="next-steps"></a>后续步骤

- 了解如何在超大规模服务器组中[启用高可用性](howto-hyperscale-high-availability.md)。
