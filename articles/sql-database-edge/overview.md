---
title: 什么是 Azure SQL 数据库边缘？ | Microsoft Docs
description: 了解 Azure SQL 数据库边缘
keywords: sql 数据库边缘简介，什么是 sql 数据库边缘，sql 数据库边缘概述
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246698"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>什么是 Azure SQL 数据库边缘预览？

Azure SQL 数据库边缘预览是一个优化的关系数据库引擎，适用于 IoT 和 IoT 边缘部署。 它提供了为 IoT 应用程序和解决方案创建高性能数据存储和处理层的功能。 Azure SQL 数据库边缘提供了流、处理和分析关系和非关系（如 JSON、图形和时间序列数据）的功能，这使得它成为各种现代 IoT 应用程序的正确选择。

Azure SQL 数据库边缘基于最新版本的 Microsoft [SQL Server 数据库引擎](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)构建，它提供了业界领先的性能、安全性和查询处理功能。 由于 Azure SQL 数据库边缘构建在 SQL 服务器和 Azure SQL 数据库的同一引擎上，它提供了相同的 T-SQL 编程表面积，使应用程序或解决方案的开发更轻松、更快速，同时使应用程序物联网边缘设备、数据中心和云之间的可移植性。

## <a name="deployment-models"></a>部署模型

Azure SQL 数据库边缘在 Azure 应用商店中可用，可以作为[Azure IoT 边缘的](../iot-edge/about-iot-edge.md)模块进行部署。 有关详细信息，请参阅部署[Azure SQL 数据库边缘](deploy-portal.md)。<br>

![SQL 数据库边缘概览图](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL 数据库边缘的版本

SQL 数据库边缘提供三个不同的版本或软件计划。 这些版本具有相同的功能集，并且仅在其使用权限及其支持的 cpu/内存量方面有所不同。

   |**计划**  |**说明**  |
   |---------|---------|
   |Azure SQL 数据库边缘开发人员  |  开发仅 SKU，每个 SQL 数据库边缘容器限制为最多 4 个内核和 32 GB 内存  |
   |Azure SQL 数据库边缘    |  生产 sKU，每个 SQL 数据库边缘容器限制为最多 8 个内核和 64 GB 内存。 |

## <a name="pricing-and-availability"></a>定价和可用性

Azure SQL 数据库当前处于预览状态。 有关定价和可用性的详细信息，请参阅 Azure [SQL 数据库边缘](https://azure.microsoft.com/services/sql-database-edge/)。

> [!IMPORTANT]
> 要了解 Azure SQL 数据库边缘和 SQL Server 之间的功能差异，以及不同 Azure SQL 数据库边缘选项之间的差异，请参阅[SQL 数据库边缘数据库功能](https://azure.microsoft.com/services/sql-database-edge/)。

## <a name="streaming-capabilities"></a>流式处理功能  

Azure SQL 数据库边缘提供内置的流功能，用于实时分析和复杂事件处理。 流式处理功能使用与[Azure 流分析](../stream-analytics/stream-analytics-introduction.md)相同的构造构建，并提供与[IoT 边缘上的 Azure 流分析](../stream-analytics/stream-analytics-edge.md)类似的功能。

Azure SQL 数据库边缘的流式处理引擎专为低延迟、恢复性、高效使用带宽和合规性而设计。

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>机器学习和人工智能功能

Azure SQL 数据库边缘通过集成开放格式 ONNX（开放神经网络交换）运行时提供内置的机器学习和分析功能，从而允许在不同框架之间交换深度学习和神经网络模型。 有关 ONNX 的详细信息，请参阅[此处](https://onnx.ai/)。 ONNX 运行时提供了使用您选择的语言或工具开发模型的灵活性，然后可以转换为 ONNX 格式，在 SQL 数据库边缘内执行。 有关详细信息，请参阅[SQL 数据库边缘中的使用 ONNX 的机器学习和人工智能](onnx-overview.md)。

## <a name="working-with-azure-sql-database-edge"></a>使用 Azure SQL 数据库边缘

Azure SQL 数据库边缘使开发和维护应用程序变得更加轻松和高效。 用户可以使用所有熟悉的工具和技能来构建出色的应用和解决方案，以满足其 IoT Edge 需求。 用户可以使用以下工具在 SQL 数据库边缘进行开发：

- [Azure 门户](https://portal.azure.com/)- 用于管理所有 Azure 服务的基于 Web 的应用程序。
- [SQL 服务器管理工作室](/sql/ssms/download-sql-server-management-studio-ssms/)- 一个免费的、可下载的客户端应用程序，用于管理从 SQL Server 到 SQL 数据库的任何 SQL 基础结构。
- [Visual Studio 中的 SQL Server 数据工具](/sql/ssdt/download-sql-server-data-tools-ssdt/)- 用于开发 SQL Server 关系数据库、SQL 数据库、集成服务包、分析服务数据模型和报告服务报告的免费、可下载的客户端应用程序。
- [Azure 数据工作室](/sql/azure-data-studio/what-is/)- 一种免费、可下载的跨平台数据库工具，适用于数据专业人员，使用 Windows、MacOS 和 Linux 上的 Microsoft 本地和云数据平台系列。
- [可视化工作室代码](https://code.visualstudio.com/docs)- 适用于 Windows、macOS 和 Linux 的免费、可下载的开源代码编辑器。 它支持各种扩展，其中包括 [mssql 扩展](https://aka.ms/mssql-marketplace)（用于查询 Microsoft SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库）。


## <a name="next-steps"></a>后续步骤

- 有关定价和可用性的详细信息，请参阅[Azure SQL 数据库边缘](https://azure.microsoft.com/services/sql-database-edge/)。
- 请求为订阅启用 Azure SQL 数据库边缘。
- 要开始，请参阅以下内容：
  - [通过 Azure 门户部署 SQL 数据库边缘](deploy-portal.md)
  - [具有 SQL 数据库边缘的机器学习和人工智能](onnx-overview.md)
