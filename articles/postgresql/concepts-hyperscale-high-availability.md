---
title: 高可用性 = 超大规模 （Citus） - 用于后格雷SQL的 Azure 数据库
description: 高可用性和灾难恢复概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975527"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 数据库中高可用性，适用于 PostgreSQL 和超大规模（Citus）

高可用性 （HA） 通过维护服务器组中每个节点的备用副本来避免数据库停机时间。 如果节点出现故障，超大规模将传入连接从故障节点切换到其备用节点。 故障转移在几分钟内发生，升级的节点始终通过 PostgreSQL 同步流复制获得新数据。

为了利用协调节点上的 HA，数据库应用程序需要检测和重试丢弃的连接和失败的事务。 新升级的协调员将使用相同的连接字符串访问。

恢复可以分为三个阶段：检测、故障转移和完全恢复。  Hyperscale 在每个节点上运行定期运行状况检查，在四次检查失败后，它确定节点已关闭。 然后，Hyperscale 将备用节点提升为主节点状态（故障转移），并设置新的备用数据库。
流式复制开始，使新节点成为最新的节点。  复制所有数据后，节点已达到完全恢复。

### <a name="next-steps"></a>后续步骤

- 了解如何在超大规模服务器组中[启用高可用性](howto-hyperscale-high-availability.md)。
