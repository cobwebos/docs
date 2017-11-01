---
title: "Azure Database for MySQL 中的高可用性概念 | Microsoft Docs"
description: "本主题介绍了使用 Azure Database for MySQL 时的高可用性概念"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 0c746aa86ac82e801c3c037116ac997c9d05706c
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2017
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的高可用性概念
Azure Database for MySQL 服务提供有保证的高级别可用性。 有资金支持的服务级别协议 (SLA) 在正式版本发布后的可用性为 99.99%。 使用此服务期间，几乎没有应用程序故障时间。

## <a name="high-availability"></a>高可用性
高可用性 (HA) 模型以节点级中断发生时的内置故障转移机制为依据。 硬件故障或响应服务部署都有可能会导致节点级中断发生。

始终必须在事务上下文中更改 MySQL。 更改会在事务提交时同步记录到 Azure 存储中。 如果发生节点级中断，数据库服务器会自动新建节点，并将数据存储附加到新节点。 这会删除任何活动连接，并且不会提交任何即时事务。

## <a name="application-retry-logic-is-essential"></a>应用程序重试逻辑至关重要
请务必生成 MySQL 数据库应用程序，用于检测和重试已删除的连接和无法执行的事务。 当应用程序重试时，应用程序的连接会在透明状态下重定向到新建实例，以取代失败实例。

在 Azure 内部，网关用于将连接重定向到新建实例。 发生中断后，整个故障转移过程一般需要几十秒才能完成。 对于客户端应用程序，外部连接字符串也是这样。

## <a name="scaling-up-or-down"></a>缩放
与 HA 模型类似，当 Azure Database for MySQL 缩放时，将会新建指定大小的服务器实例。 现有数据存储会从原始实例中拆离，并附加到新实例中。

执行缩放操作期间，数据库连接会中断。 客户端应用程序的连接中断，未提交的未结事务也会遭取消。 在客户端应用程序重试连接或建立新连接后，网关便会将连接定向到新设置大小的实例。 

## <a name="next-steps"></a>后续步骤
- 有关该服务的概述，请参阅 [Azure Database for MySQL 概述](overview.md)