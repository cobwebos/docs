---
title: Azure Database for MySQL 中的只读副本。
description: 本文介绍 Azure Database for MySQL 的只读副本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888458"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的只读副本

使用只读副本功能，可以将数据从 Azure Database for MySQL 服务器（主）复制到同一 Azure 区域中最多五个只读服务器（副本）。 使用 MySQL 引擎的基于本机二进制日志 (binlog) 文件位置的复制技术以异步方式更新只读副本。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

Azure Database for MySQL 服务中创建的副本是新服务器，可以像常规/独立 MySQL 服务器一样进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及预配的每月 GB 存储量计费。

如需了解有关 MySQL 复制功能和问题的详细信息，请参阅 [MySQL 复制文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)。

## <a name="when-to-use-read-replicas"></a>何时使用只读副本

读取密集型的应用程序和工作负载可通过只读副本提供服务。 与计划仅使用一台服务器进行读取和写入相比，只读副本有助于增加可用的读取容量。 读取工作负载可以与副本服务器隔离，而写入工作负载可以定向到主服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

## <a name="considerations-and-limitations"></a>注意事项和限制

### <a name="pricing-tiers"></a>定价层

只读副本当前仅适用于“常规用途”和“内存优化”的定价层。

### <a name="master-server-restart"></a>主服务器重启

时有任何现有副本 master 数据库中创建的副本，主将第一次重新启动以准备用于复制的本身。 请考虑这一点并在非高峰期执行这些操作。

### <a name="stopping-replication"></a>停止复制

可以选择停止主服务器和副本服务器之间的复制。 停止复制会删除主服务器和副本服务器之间的复制关系。

停止复制后，副本服务器将成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本服务器上可用的数据。 独立服务器与主服务器不同步。 此服务器不能再次成为到副本服务器。

### <a name="replicas-are-new-servers"></a>副本服务器是新服务器

将副本服务器创建为新的 Azure Database for MySQL 服务器。 现有服务器不能成为副本服务器。

### <a name="replica-server-configuration"></a>副本服务器配置

使用与主服务器相同的服务器配置创建副本服务器，其中包括以下配置：

- 定价层
- 计算的代
- vCore 数
- 存储
- 备份保留期
- 备份冗余选项
- MySQL 引擎版本
- 防火墙规则

创建副本服务器后，你可以更改定价层（基本除外）、计算代系、vCore 数、存储和独立于主服务器的备份保留。

### <a name="master-server-configuration"></a>主服务器配置

如果已更新主服务器配置（例如 vCore 数或存储），则还应将副本服务器的配置更新为等值或更大的值。 如果没有更新，副本服务器可能无法与对主服务器所做的更改保持同步，并且可能会崩溃。

创建副本服务器后添加到主服务器的新防火墙规则不会复制到副本。 还应使用此新防火墙规则更新副本。

### <a name="deleting-the-master-server"></a>删除主服务器

删除主服务器后，将对所有只读副本停止复制。 这些副本服务器将成为独立服务器。 将删除主服务器本身。

### <a name="user-accounts"></a>用户帐户

主服务器上的用户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

### <a name="other"></a>其他

- 不支持全局事务标识符 (GTID)。
- 不支持创建副本服务器的副本。
- 内存中的表可能会导致副本服务器变得不同步。这是 MySQL 复制技术的限制。 有关详细信息，请阅读 [MySQL 参考文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)中的更多信息。
- 创建副本服务器后优化主服务器上的 [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 参数可能会导致副本服务器变得不同步。副本服务器并不知道其他表空间。
- 确保主服务器表具有主键。 缺少主键可能会导致主服务器与副本服务器之间的复制延迟。
- 查看 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)中 MySQL 复制限制的完整列表


## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 门户创建和管理只读副本](howto-read-replicas-portal.md)
- 了解如何[使用 Azure CLI 创建和管理只读副本](howto-read-replicas-cli.md)
