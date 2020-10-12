---
title: 监视和指标-Azure Database for PostgreSQL 灵活的服务器
description: 本文介绍 Azure Database for PostgreSQL 灵活的服务器中的监视和指标功能。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1519e0b5cef6055cf8d8b0aded0d8ad323d548a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707840"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>监视 Azure Database for PostgreSQL 灵活服务器上的指标

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 Azure Database for PostgreSQL 提供各种监视选项，帮助用户深入了解服务器的行为。

## <a name="metrics"></a>指标
Azure Database for PostgreSQL 提供了各种指标来帮助用户深入了解为 PostgreSQL 服务器提供支持的资源的行为。 每项指标以一分钟为频率发出，[历史记录长达 93 天](../../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics)。 可针对指标配置警报。 其他选项包括设置自动操作、执行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../../azure-monitor/platform/data-platform-metrics.md)。

### <a name="list-of-metrics"></a>指标列表
以下指标适用于 PostgreSQL 挠性服务器：


|指标|指标显示名称|计价单位|说明|
|---|---|---|---|
| active_connections | 活动连接数 | Count | 与服务器的连接数。 | 
| backup_storage_used | 使用的备份存储 | 字节 | 使用的备份存储量。 此指标表示根据为服务器设置的备份保留期保留的所有完整数据库备份、差异备份和日志备份所消耗的存储的总和。 备份的频率为服务管理。 对于异地冗余存储，备份存储使用率是本地冗余存储的两倍。 |
| connections_failed | 失败的连接数 | Count | 连接失败。 |
| connections_succeeded | 成功连接数 | 计数 | 已成功连接。 |
| cpu_credits_consumed | 已用 CPU 信用额度 | Count | 灵活服务器使用的信用额度。 适用于可突增层。 |
| cpu_credits_remaining | 剩余 CPU 信用额度 | Count | 可用于突发的富余点数。 适用于可突增层。 |
| cpu_percent | CPU 百分比 | 百分比 | 使用的 CPU 百分比。 | 
| disk_queue_depth | 磁盘队列深度 | Count | 数据磁盘的未完成 i/o 操作的数量。 |
| iops | IOPS | Count | 每秒磁盘 i/o 操作数。 |
| maximum_used_transactionIDs | 最大已用事务 ID 数 | 计数 | 使用的最大事务 ID。 |
| memory_percent | 内存百分比 | 百分比 | 使用的内存百分比。 |
| network_bytes_egress | 网络传出 | 字节 | 传出的网络流量。 |
| network_bytes_ingress | 网络传入 | 字节 | 传入的网络流量。 |
| read_iops | 读取 IOPS | Count | 每秒数据磁盘 i/o 读取操作数。 |
| read_throughput | 读取吞吐量 | 字节 | 每秒从磁盘读取的字节数。 |
| storage_free | 可用存储 | 字节 | 可用存储空间量。 |
| storage_percent | 存储空间百分比 | 百分比 | 已用存储空间的百分比。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|
| storage_used | 使用的存储 | 字节 | 已用存储空间的百分比。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。 |
| txlogs_storage_used | 使用的事务日志存储 | 字节 | 事务日志使用的存储空间量。 | 
| write_throughput | 写入吞吐量 | 字节 | 每秒写入磁盘的字节数。 |
| write_iops | 写入 IOPS | Count | 每秒数据磁盘 i/o 写入操作数。 |

## <a name="server-logs"></a>服务器日志
Azure Database for PostgreSQL 允许配置和访问 Postgres 的标准日志。 若要了解有关日志的详细信息，请访问 [日志记录概念文档](concepts-logging.md)。
