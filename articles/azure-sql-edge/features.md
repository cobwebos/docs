---
title: Azure SQL Edge 支持的功能
description: 详细了解 Azure SQL Edge 支持的功能。
keywords: SQL Edge 简介, 什么是 SQL Edge, SQL Edge 概述
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 646ce94587a9aa1bb8fd20a28b84658994b25cf1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886538"
---
# <a name="supported-features-of-azure-sql-edge"></a>Azure SQL Edge 支持的功能 

Azure SQL Edge 构建在最新版本的 SQL 数据库引擎上。 除了当前在 Linux 上的 SQL Server 2019（或 Windows 上的 SQL Server）中不支持或尚未提供的某些功能外，它还支持 Linux 上的 SQL Server 2019 中支持的一部分功能。

有关 Linux 上的 SQL Server 中支持的功能的完整列表，请参阅 [Linux 上的 SQL Server 2019 的版本和支持功能](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)。 有关 Windows 上 SQL Server 的版本和支持的功能，请参阅 [SQL Server 2019 (15.x) 的版本和支持的功能](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15)。

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge 版本

Azure SQL Edge 有两个不同的版本或软件计划。 这些版本具有相同的功能集，并且仅在使用权限和它们在主机系统上可以访问的内存量和内核数方面存在差异。

   |**规划**  |**说明**  |
   |---------|---------|
   |Azure SQL Edge 开发人员  |  仅用于开发。 每个 Azure SQL Edge 开发人员版容器最多只能有 4 个内核和 32 GB 内存。  |
   |Azure SQL Edge    |  用于生产。 每个 Azure SQL Edge 容器最多只能有 8 个内核和 64 GB 内存。  |

## <a name="operating-system"></a>操作系统

Azure SQL Edge 容器基于 Ubuntu 18.04，因此只能在运行 Ubuntu 18.04 LTS（建议）或 Ubuntu 20.04 LTS 的 Docker 主机上运行。 Azure SQL Edge 容器可以在其他操作系统主机上运行，例如，它可以在 Linux 的其他分发版上或 Windows 上运行（使用 Docker CE 或 Docker EE），但 Microsoft 不建议这样做，因为此配置可能没有经过广泛测试。

在 Windows 上运行 Azure SQL Edge 的建议配置是在 Windows 主机上配置 Ubuntu VM，然后在 Linux VM 内运行 Azure SQL Edge。

为 Azure SQL Edge 推荐和支持的文件系统为 EXT4 和 XFS。 如果使用永久性卷来备份 Azure SQL Edge 数据库存储，则基础主机文件系统必须是 EXT4 和 XFS。

## <a name="hardware-support"></a>硬件支持

Azure SQL Edge 需要 64 位处理器（x64 或 ARM64），并且主机上至少有一个处理器和 1 GB RAM。 虽然 Azure SQL Edge 的启动内存占用接近 450 MB，但其他在边缘设备上运行的 IoT Edge 模块或进程需要更多内存。 Azure SQL Edge 的实际内存和 CPU 要求取决于工作负荷的复杂性和正在处理的数据量。 为解决方案选择硬件时，Microsoft 建议运行广泛的性能测试，以确保满足解决方案所需的性能特征。  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge 组件

Azure SQL Edge 仅支持数据库引擎。 它不支持 Windows 或 Linux 上的 SQL Server 2019 提供的其他组件。 具体而言，Azure SQL Edge 不支持 SQL Server 组件，如 Analysis Services、Reporting Services、Integration Services、Master Data Services、机器学习服务（数据库内）和 Machine Learning Server（独立）。

## <a name="supported-features"></a>支持的功能

除了支持 Linux 上 SQL Server 的一部分功能以外，Azure SQL Edge 还支持以下新功能： 

- SQL 流式处理基于支持 Azure 流分析的同一个引擎，用于在 Azure SQL Edge 中提供实时数据流式处理功能。 
- 用于时序数据分析的 T-SQL 函数调用 `Date_Bucket`。
- 通过 ONNX 运行时，机器学习功能包含在 SQL 引擎中。

## <a name="unsupported-features"></a>不支持的功能

以下列表包含 Azure SQL Edge 当前不支持的 Linux 上的 SQL Server 2019 功能。

| 区域 | 不支持的功能或服务 |
|-----|-----|
| **数据库设计** | 内存中 OLTP 以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图。 |
| &nbsp; | `HierarchyID` 数据类型以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图。 |
| &nbsp; | `Spatial` 数据类型以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图。 |
| &nbsp; | Stretch DB 以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图。 |
| &nbsp; | 全文检索和搜索以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图。|
| &nbsp; | `FileTable`、`FILESTREAM` 以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图。|
| **数据库引擎** | 复制。 请注意，可以将 Azure SQL Edge 配置为复制拓扑的推送订阅服务器。 |
| &nbsp; | Polybase。 请注意，可以将 Azure SQL Edge 配置为 Polybase 中外部表的目标。 |
| &nbsp; | 通过 Java 和 Spark 实现语言扩展性。 |
| &nbsp; | Active Directory 集成。 |
| &nbsp; | 数据库自动收缩。 可以使用 `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` 命令设置数据库的自动收缩属性，但是该更改不起作用。 自动收缩任务不会针对数据库运行。 用户仍可使用“DBCC”命令收缩数据库文件。 |
| &nbsp; | 数据库快照。 |
| &nbsp; | 支持永久性内存。 |
| &nbsp; | Microsoft 分布式事务处理协调器。 |
| &nbsp; | Resource Governor 和 IO 资源调控。 |
| &nbsp; | 缓冲池扩展。 |
| &nbsp; | 具有第三方连接的分布式查询。 |
| &nbsp; | 链接服务器。 |
| &nbsp; | 系统扩展存储过程（如 `XP_CMDSHELL`）。 |
| &nbsp; | CLR 程序集以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图。 |
| &nbsp; | CLR 相关的 T-SQL 函数，例如 `ASSEMBLYPROPERTY`、`FORMAT`、`PARSE` 和 `TRY_PARSE`。 |
| &nbsp; | CLR 相关的日期和时间目录视图、函数和查询子句。 |
| &nbsp; | 缓冲池扩展。 |
| &nbsp; | 数据库邮件。 |
| &nbsp; | Service Broker |
| &nbsp; | 基于策略的管理 |
| &nbsp; | 管理数据仓库 |
| &nbsp; | 包含的数据库 |
| **SQL Server 代理** |  子系统：CmdExec、PowerShell、队列读取器、SSIS、SSAS 和 SSRS。 |
| &nbsp; | 警报。 |
| &nbsp; | 托管备份。 |
| **高可用性** | Always On 可用性组。  |
| &nbsp; | 基本可用性组。 |
| &nbsp; | Always On 故障转移群集实例。 |
| &nbsp; | 数据库镜像。 |
| &nbsp; | 热插拔内存和 CPU。 |
| **安全性** | 可扩展的密钥管理。 |
| &nbsp; | Active Directory 集成。|
| &nbsp; | 支持安全 Enclave。|
| **服务** | SQL Server Browser。 |
| &nbsp; | 通过 R 和 Python 进行机器学习。 |
| &nbsp; | StreamInsight。 |
| &nbsp; | Analysis Services。 |
| &nbsp; | Reporting Services。 |
| &nbsp; | Data Quality Services。 |
| &nbsp; | Master Data Services。 |
| &nbsp; | Distributed Replay、 |
| **可管理性** | SQL Server 实用工具控制点。 |

## <a name="next-steps"></a>后续步骤

- [部署 Azure SQL Edge](deploy-portal.md)
- [配置 Azure SQL Edge](configure.md)
- [使用 SQL Server 客户端工具连接到 Azure SQL Edge 实例](connect.md)
- [在 Azure SQL Edge 中备份和还原数据库](backup-restore.md)
