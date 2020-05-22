---
title: 什么是 Azure SQL Edge（预览版）？
description: 了解 Azure SQL Edge（预览版）
keywords: SQL Edge 简介, 什么是 SQL Edge, SQL Edge 概述
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6e5e54f7bf61d933240403ff952c1aa6e162958f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594576"
---
# <a name="what-is-azure-sql-edge-preview"></a>什么是 Azure SQL Edge（预览版）？

Azure SQL Edge（预览版）是已针对 IoT 和 IoT Edge 部署进行了优化的关系数据库引擎。 它提供为 IoT 应用程序和解决方案创建高性能数据存储和处理层的功能。 Azure SQL Edge 提供了流式传输、处理和分析关系和非关系数据（如 JSON、图形和时序数据）的功能，这使其成为各种新式 IoT 应用程序的正确选择。

Azure SQL Edge 基于最新版本的 [Microsoft SQL Server 数据库引擎](/sql/sql-server/sql-server-technical-documentation?toc=/azure/azure-sql-edge/toc.json)而构建，提供行业领先的性能、安全性和查询处理功能。 由于 Azure SQL Edge 建立在与 SQL Server 和 Azure SQL 数据库相同的引擎上，因此它提供相同的 T-SQL 编程外围应用，使应用程序或解决方案的开发更简单且快速，同时使应用程序在 IoT Edge 设备、数据中心和云之间的可移植性变得简单。

> [!NOTE]
> Azure SQL Edge 目前以预览版形式提供，因此不应在生产环境中使用。

## <a name="deployment-models"></a>部署模型

Azure SQL Edge 在 Azure 市场中提供，可以部署为 [Azure IoT Edge](../iot-edge/about-iot-edge.md) 模块。 有关详细信息，请参阅[部署 Azure SQL Edge](deploy-portal.md)。<br>

![SQL Edge 概述关系图](media/overview/overview.png)

## <a name="editions-of-sql-edge"></a>SQL Edge 版本

SQL Edge 有三个不同的版本或软件计划。 这些版本具有相同的功能集，仅在使用权限和支持的 CPU/内存量方面存在差异。

   |**规划**  |**说明**  |
   |---------|---------|
   |Azure SQL Edge 开发人员  |  仅限开发 SKU，每个 SQL Edge 容器最多只能有 4 个内核和 32 GB 内存  |
   |Azure SQL Edge    |  生产 SKU，每个 SQL Edge 容器最多只能有 8 个内核和 64 GB 内存。 |

## <a name="pricing-and-availability"></a>定价和可用性

Azure SQL Edge 目前处于预览状态。 有关定价和可用性的详细信息，请参阅 [Azure SQL Edge](https://azure.microsoft.com/services/sql-database-edge/)。

> [!IMPORTANT]
> 若要了解 Azure SQL Edge 与 SQL Server 的功能差异，以及不同 Azure SQL Edge 选项之间的区别，请参阅[Azure SQL Edge 支持的功能](features.md)。

## <a name="streaming-capabilities"></a>流式处理功能  

Azure SQL Edge 提供内置的流式处理功能，可用于实时分析和复杂事件处理。 流式处理功能是使用与 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)相同的构造构建的，其功能类似于 [Azure IoT Edge 流分析](../stream-analytics/stream-analytics-edge.md)。

Azure SQL Edge 的流式处理引擎专为实现低延迟、复原能力、有效使用带宽和合规性而设计。 

有关 SQL Edge 中的数据流式处理的详细信息，请参阅[数据流式处理](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>机器学习和人工智能功能

Azure SQL Edge 通过集成开放格式 ONNX (Open Neural Network Exchange) 运行时来提供内置的机器学习和分析功能，这允许在不同框架之间交换深度学习和神经网络模型。 有关 ONNX 的详细信息，请参阅[此处](https://onnx.ai/)。 ONNX 运行时具有灵活性，可在你选择的语言或工具中开发模型，然后将其转换为 ONNX 格式，以便在 SQL Edge 内执行。 有关详细信息，请参阅 [在 SQL Edge 中使用 ONNX 实现机器学习和人工智能](onnx-overview.md)。

## <a name="working-with-azure-sql-edge"></a>使用 Azure SQL Edge

Azure SQL Edge 使应用程序的开发和维护更加轻松、高效。 用户可以使用所有熟悉的工具和技能来构建功能强大的应用和解决方案，以满足其 IoT Edge 需求。 用户可以使用如下工具在 SQL Edge 中开发：

- [Azure 门户](https://portal.azure.com/) - 一个基于 Web 的应用程序，用于管理所有 Azure 服务。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - 一款免费、可下载的客户端应用程序，用于管理任何 SQL 基础结构（从 SQL Server 到 SQL 数据库，不一而足）。
- [Visual Studio 中的 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt/) - 一个免费、可下载的客户端应用程序，用于开发 SQL Server 关系数据库、SQL 数据库、Integration Services 包、Analysis Services 数据模型和 Reporting Services 报表。
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - 一个免费、可下载的跨平台数据库工具，适用于在 Windows、macOS 和 Linux 上使用 Microsoft 系列本地和云数据平台的数据专业人员。
- [Visual Studio Code](https://code.visualstudio.com/docs) - 一个免费、可下载的开放源代码代码编辑器，适用于 Windows、macOS 和 Linux。 它支持各种扩展，其中包括 [mssql 扩展](https://aka.ms/mssql-marketplace)（用于查询 Microsoft SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库）。


## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL Edge](deploy-portal.md)
- [SQL Edge 中的机器学习和人工智能](onnx-overview.md)
- [使用 SQL Edge 构建端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)
