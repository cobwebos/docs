---
title: "Azure Database for PostgreSQL 中的高可用性概念 | Microsoft Docs"
description: "本主题提供使用 Azure Database for PostgreSQL 时的高可用性信息"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 315eb32a293eec89b274ccd017dba506241533e6
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2017
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的高可用性概念
Azure Database for PostgreSQL 服务提供有保证的高级别可用性。 财务备份服务级别协议 (SLA) 一定是通用版本。 在使用此服务时几乎没有应用程序停机时间。

## <a name="high-availability"></a>高可用性
在发生节点级别中断时，高可用性 (HA) 模型便基于内置故障转移机制。 硬件故障或响应服务部署均可导致发生节点级别中断。

对 PostgreSQL 所做的更改将始终出现在事务的上下文中。 在提交事务时，将在 Azure 存储中同步记录所做的更改。 如果出现节点级别中断，数据库服务器将自动创建一个新的节点，并将数据存储附加到新的节点。 会删除任何活动连接，且不会提交任何即时事务。

## <a name="application-retry-logic-is-essential"></a>应用程序重试逻辑至关重要
必须构建 PostgreSQL 数据库应用程序，以检测和重试已删除的连接和失败的事务，这一点很重要。 应用程序重试时，应用程序的连接将以透明的方式被重定向到新建的实例，此实例接管失败实例。

在 Azure 内部，网关用于将连接重定向到新实例。 出现中断时，整个故障转移过程一般需要几十秒。 客户端应用程序的外部连接字符串仍然相同。

## <a name="scaling-up-or-down"></a>横向扩展或缩减
与 HA 模型类似，在 Azure Database for PostgreSQL 横向扩展或缩减时，将创建具有指定大小的新服务器实例。 现有数据存储从原始实例中分离出来，并被附加到新实例中。

进行缩放操作时，会出现数据库连接中断。 客户端应用程序已断开连接，并取消打开未提交的事务。 客户端应用程序重试连接或建立新连接后，网关将该连接定向到具有新大小的实例。 

## <a name="next-steps"></a>后续步骤
- 有关该服务的概述，请参阅 [Azure Database for PostgreSQL 概述](overview.md)
