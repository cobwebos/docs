---
title: Azure SQL Edge（预览版）支持的功能
description: 了解 Azure SQL Edge 支持的功能的详细信息 (预览版) 。
keywords: SQL Edge 简介, 什么是 SQL Edge, SQL Edge 概述
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 87ccb28f527082dccee338396b460124652c0e76
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462694"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Azure SQL Edge（预览版）支持的功能 

Azure SQL Edge 构建于 Linux 上最新版本的 Microsoft SQL Server 数据库引擎。 除了在 Linux (上的 SQL Server 2019 或 Windows) 的 SQL Server 中当前不支持或未提供某些功能，它还支持 Linux 上的 SQL Server 2019 中支持的功能子集。

有关 Linux 上的 SQL Server 中支持的功能的完整列表，请参阅 [Linux 上的 SQL Server 2019 的版本和支持功能](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)。 有关 Windows 上的 SQL Server 的版本和支持的功能，请参阅 [SQL Server 2019 () 的版本和支持的功能 ](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15)。

> [!NOTE]
> Azure SQL Edge 目前处于预览阶段，因此不应在生产环境中使用。 Microsoft 可能建议在生产环境中运行 Azure SQL Edge，具体取决于部署验证和用例方案。

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge 版本

Azure SQL Edge 有两个不同的版本或软件计划。 这些版本具有相同的功能集，并且仅在其使用权限和它们可在主机系统上访问的内存和内核数量之间有所不同。

   |**规划**  |**说明**  |
   |---------|---------|
   |Azure SQL Edge 开发人员  |  仅用于开发。 每个 Azure SQL Edge 开发人员容器最多支持4个核心和 32 GB 内存。  |
   |Azure SQL Edge    |  用于生产。 每个 Azure SQL Edge 容器最多只能有8个核心和 64 GB 内存。  |

## <a name="operating-system"></a>操作系统

Azure SQL Edge 容器目前基于 Ubuntu 16.04，因此仅支持在运行 Ubuntu 16.04 LTS 的 Docker 主机上运行 (建议) 或 Ubuntu 18.04 LTS。 例如，可以在其他操作系统主机上运行 Azure SQL Edge 容器，例如，它可以在其他 Linux 分发版上运行，也可以使用 Docker CE 或 Docker EE) 在 Windows (上运行，但 Microsoft 不建议这样做，因为此配置可能不会经过广泛测试。

目前仅支持通过 Azure IoT Edge 来部署 Azure SQL Edge。 有关详细信息，请参阅 [Azure IoT Edge 支持的系统](https://docs.microsoft.com/azure/iot-edge/support)。

在 Windows 上运行 Azure SQL Edge 的建议配置是在 Windows 主机上配置 Ubuntu VM，然后在 Linux VM 中运行 Azure SQL Edge。

为 Azure SQL Edge 推荐和支持的文件系统为 EXT4 和 XFS。 如果使用永久性卷来备份 Azure SQL Edge 数据库存储，则基础主机文件系统需要是 EXT4 和 XFS。

## <a name="hardware-support"></a>硬件支持

Azure SQL Edge 要求64位处理器 (x64 或 ARM64) ，并且主机上至少有一个处理器和一个 GB RAM。 虽然 Azure SQL Edge 的启动内存占用接近 500 MB，但其他在边缘设备上运行的 IoT Edge 模块需要更多内存。 Azure SQL Edge 的实际内存和 CPU 要求将取决于工作负荷和正在处理的数据量的复杂性。 为解决方案选择硬件时，Microsoft 建议运行广泛的性能测试，以确保满足解决方案所需的性能特征。  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge 组件

Azure SQL Edge 仅支持数据库引擎。 它不包含对 Windows 上的 SQL Server 2019 或 Linux 上 SQL Server 2019 的其他组件的支持。 具体而言，Azure SQL Edge 不支持 SQL Server 组件，如 Analysis Services、Reporting Services、Integration Services、Master Data Services、机器学习服务 (数据库内) ，以及 Machine Learning Server 的独立 () 。

## <a name="supported-features"></a>支持的功能

除了支持 Linux 上的 SQL Server 功能的子集以外，Azure SQL Edge 还支持以下新功能： 

- SQL 流式处理基于支持 Azure 流分析的同一个引擎，提供 Azure SQL Edge 中的实时数据流式处理功能。 
- `Date_Bucket`用于时序数据分析的 t-sql 函数调用。
- ONNX 运行时中的机器学习功能，随 SQL 引擎一起提供。

## <a name="unsupported-features"></a>不支持的功能

以下列表包含 Azure SQL Edge 当前不支持的 Linux 功能上的 SQL Server 2019。

| 区域 | 不支持的功能或服务 |
|-----|-----|
| **数据库设计** | 内存中 OLTP 以及相关的 DDL 命令和 Transact-sql 函数、目录视图和动态管理视图。 |
| &nbsp; | `HierarchyID` 数据类型，以及相关的 DDL 命令和 Transact-sql 函数、目录视图和动态管理视图。 |
| &nbsp; | `Spatial` 数据类型，以及相关的 DDL 命令和 Transact-sql 函数、目录视图和动态管理视图。 |
| &nbsp; | Stretch DB、相关的 DDL 命令和 Transact-sql 函数、目录视图和动态管理视图。 |
| &nbsp; | 全文索引和搜索，以及相关的 DDL 命令和 Transact-sql 函数、目录视图和动态管理视图。|
| &nbsp; | `FileTable`、 `FILESTREAM` 和相关的 DDL 命令和 transact-sql 函数、目录视图和动态管理视图。|
| **数据库引擎** | 复制。 请注意，可以将 Azure SQL Edge 配置为复制拓扑的推送订阅服务器。 |
| &nbsp; | Polybase。 请注意，可以将 Azure SQL Edge 配置为 Polybase 中外部表的目标。 |
| &nbsp; | 通过 Java 和 Spark 进行语言扩展性。 |
| &nbsp; | Active Directory 集成。 |
| &nbsp; | 数据库快照。 |
| &nbsp; | 支持永久性内存。 |
| &nbsp; | Microsoft 分布式事务处理协调器。 |
| &nbsp; | 资源调控器和 IO 资源调控。 |
| &nbsp; | 缓冲池扩展。 |
| &nbsp; | 带有第三方连接的分布式查询。 |
| &nbsp; | 链接服务器。 |
| &nbsp; | 系统扩展存储过程 (如 `XP_CMDSHELL`) 。 |
| &nbsp; | CLR 程序集，以及相关的 DDL 命令和 Transact-sql 函数、目录视图和动态管理视图。 |
| &nbsp; | CLR 相关的 t-sql 函数，例如 `ASSEMBLYPROPERTY` 、、 `FORMAT` `PARSE` 和 `TRY_PARSE` 。 |
| &nbsp; | 与 CLR 相关的日期和时间目录视图、函数和查询子句。 |
| &nbsp; | 缓冲池扩展。 |
| &nbsp; | 数据库邮件。 |
| &nbsp; | Service Broker |
| &nbsp; | 基于策略的管理 |
| &nbsp; | 管理数据仓库 |
| &nbsp; | 包含的数据库 |
| **SQL Server 代理** |  子系统： CmdExec、PowerShell、队列读取器、SSIS、SSAS 和 SSRS。 |
| &nbsp; | 提醒. |
| &nbsp; | 托管备份。 |
| **高可用性** | Always On 可用性组。  |
| &nbsp; | 基本可用性组。 |
| &nbsp; | Always On 故障转移群集实例。 |
| &nbsp; | 数据库镜像。 |
| &nbsp; | 热添加内存和 CPU。 |
| **安全性** | 可扩展的密钥管理。 |
| &nbsp; | Active Directory 集成。|
| &nbsp; | 支持安全 enclaves。|
| **服务** | SQL Server Browser。 |
| &nbsp; | 通过 R 和 Python 机器学习。 |
| &nbsp; | StreamInsight. |
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
