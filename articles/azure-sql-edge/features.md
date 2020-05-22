---
title: Azure SQL Edge（预览版）支持的功能
description: 详细了解 Azure SQL Edge（预览版）支持的功能
keywords: SQL Edge 简介, 什么是 SQL Edge, SQL Edge 概述
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7d33c2bef1cd0f7bfab4ec3c09f16c049881d1cd
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594616"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Azure SQL Edge（预览版）支持的功能 

本文详细介绍了 Azure SQL Edge 支持的功能。 Azure SQL Edge 在 Linux 最新版本的 Microsoft SQL Server 数据库引擎的基础上构建，除了当前在 Linux 或 Windows 上的 SQL Server 2019 中不支持或尚未提供的某些功能，它支持适用于 Linux 的 SQL Server 2019 中的一个功能子集。 有关 Linux 上的 SQL Server 中支持的功能的完整列表，请参阅 [Linux 上的 SQL Server 2019 的版本和支持功能](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)。 有关 Windows 上 SQL Server 的版本和支持功能，请参阅 [SQL Server 2019 (15.x) 的版本和支持功能](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15)。

> [!NOTE]
> Azure SQL Edge 目前以预览版形式提供，因此不应在生产环境中使用。 在验证部署和用例方案后，Microsoft 可能会建议在生产环境中运行 Azure SQL Edge。

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge 版本

Azure SQL Edge 有两个不同的版本或软件计划。 这些版本具有相同的功能集，并且仅在使用权限和它们在主机系统上可以访问的 CPU/内存量方面存在差异。

   |**规划**  |**说明**  |
   |---------|---------|
   |Azure SQL Edge 开发人员  |  仅开发 SKU，每个 Azure SQL Edge 开发人员容器最多只能有 4 个内核和 32 GB 内存  |
   |Azure SQL Edge    |  生产 SKU，每个 Azure SQL Edge 容器最多只能有 8 个内核和 64 GB 内存  |

## <a name="operating-system"></a>操作系统

Azure SQL Edge 容器目前基于 Ubuntu（16.04 和18.04），因此仅支持在运行 Ubuntu 16.04 和 18.04 的 docker 主机上运行。 Azure SQL Edge 还可以在其他操作系统主机上运行，例如，Linux 的其他分发版或在 Windows 上（使用 Docker CE 或 Docker EE），但 Microsoft 未对这些配置进行广泛测试。

目前仅支持通过 Azure IoT Edge 来部署 Azure SQL Edge。 有关 Azure IoT Edge 支持的系统的详细信息，请参阅 [Azure IoT Edge 支持的系统](https://docs.microsoft.com/azure/iot-edge/support)。

在 Windows 上运行 Azure SQL Edge 的建议配置是，在 Windows 主机上配置 Ubuntu VM，然后在该 Linux VM 内运行 SQL Edge。

## <a name="hardware-support"></a>硬件支持

Azure SQL Edge 需要 64 位处理器，处理器可以是 Intel、AMD 或 ARM，并且主机上至少有一个处理器和 1 GB RAM。 虽然 Azure SQL Edge 的启动内存占用接近 500 MB，但其他在边缘设备上运行的 IoT Edge 模块需要更多内存。

## <a name="azure-sql-edge-components"></a>Azure SQL Edge 组件

Azure SQL Edge 仅支持数据库引擎，不支持 Windows 或 Linux 上的 SQL Server 2019 提供的其他组件。 具体而言，Azure SQL Edge 不支持 SQL Server 组件，如 Analysis Services、Reporting Services、Integration Services、Master Data Services、机器学习服务（数据库内）和 Machine Learning Server（独立）。

## <a name="supported-features"></a>支持的功能

除了支持 Linux 上 SQL Server 功能的子集以外，Azure SQL Edge 还支持以下新功能。 

- SQL 流式处理基于支持 Azure 流分析的同一个引擎，在 Azure SQL Edge 中提供实时数据流式处理功能。 
- 新的 T-SQL 函数调用 Date_Bucket 进行时序数据分析。
- 通过 ONNX 运行时，机器学习功能包含在 SQL 引擎中。

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Azure SQL Edge 不支持 Linux 上的 SQL Server 功能

以下列表包含 Azure SQL Edge 当前不支持的 Linux 上的 SQL Server 2019 功能。

| 区域 | 不支持的功能或服务 |
|-----|-----|
| **数据库设计** | 内存中 OLTP 以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图 |
| &nbsp; | HierarchyID 数据类型以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图 |
| &nbsp; | 空间数据类型以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图 |
| &nbsp; | Stretch DB 以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图 |
| &nbsp; | 全文本索引和搜索以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图|
| &nbsp; | FileTable、FILESTREAM 以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图|
| **数据库引擎** | 复制。 不过，可以将 Azure SQL Edge 配置为复制拓扑的推送订阅服务器。 |
| &nbsp; | Polybase。 不过，可以将 Azure SQL Edge 配置为 Polybase 中外部表的目标 |
| &nbsp; | 通过 Java 和 Spark 实现语言扩展性 |
| &nbsp; | Active Directory 集成 |
| &nbsp; | 数据库快照 |
| &nbsp; | 对持久性内存的支持 |
| &nbsp; | Microsoft 分布式事务处理协调器 |
| &nbsp; | Resource Governor 和 IO 资源调控 |
| &nbsp; | 缓冲池扩展 |
| &nbsp; | 具有第三方连接的分布式查询 |
| &nbsp; | 链接服务器 |
| &nbsp; | 系统扩展存储过程（XP_CMDSHELL 等） |
| &nbsp; | CLR 程序集以及相关的 DDL 命令、Transact-SQL 函数、目录视图和动态管理视图 |
| &nbsp; | CLR 相关的 T-SQL 函数，例如 ASSEMBLYPROPERTY、FORMAT、PARSE、TRY_PARSE |
| &nbsp; | CLR 相关的日期和时间目录视图、函数和查询子句 |
| &nbsp; | 缓冲池扩展 |
| &nbsp; | 数据库邮件 |
| **SQL Server 代理** |  子系统：CmdExec、PowerShell、队列读取器、SSIS、SSAS、SSRS |
| &nbsp; | 警报 |
| &nbsp; | 托管备份 |
| **高可用性** | AlwaysOn 可用性组  |
| &nbsp; | 基本可用性组 |
| &nbsp; | AlwaysOn 故障转移群集实例 |
| &nbsp; | 数据库镜像 |
| &nbsp; | 热插拔内存和 CPU |
| **安全性** | 可扩展的密钥管理 |
| &nbsp; | Active Directory 集成|
| &nbsp; | 支持安全 Enclave|
| **服务** | SQL Server Browser |
| &nbsp; | 通过 R 和 Python 进行机器学习 |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | 数据库引擎服务 |
| &nbsp; | Master Data Services |
| &nbsp; | 分布式重播 |
| **可管理性** | SQL Server 实用工具控制点 |

## <a name="next-steps"></a>后续步骤

- [部署 Azure SQL Edge](deploy-portal.md)
- [配置 Azure SQL Edge](configure.md)
- [使用 SQL Server 客户端工具连接到 Azure SQL Edge 实例](connect.md)
- [Azure SQL Edge 中的备份和还原数据库功能](backup-restore.md)
