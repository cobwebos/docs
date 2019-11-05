---
title: 什么是 Azure SQL 数据库边缘？ | Microsoft 文档
description: 了解 Azure SQL 数据库边缘
keywords: sql 数据库边缘简介，什么是 sql 数据库边缘，sql 数据库边缘概述
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9cbfc17e7412b4d30f082354996721ee7b5d6d5b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513999"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>什么是 Azure SQL 数据库边缘预览版？

Azure SQL 数据库边缘预览版是一种优化的关系数据库引擎，适用于 IoT 和 IoT Edge 部署。 它提供为 IoT 应用程序和解决方案创建高性能数据存储和处理层的功能。 Azure SQL 数据库边缘提供流式处理、处理和分析关系和非关系（如 JSON、图形和时间序列数据）的功能，这使其成为各种新式 IoT 应用程序的正确选择。

Azure SQL 数据库边缘构建在最新版本的[Microsoft SQL Server 数据库引擎](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)上，提供业界领先的性能、安全性和查询处理功能。 由于 Azure SQL 数据库边缘构建在与 SQL Server 和 Azure SQL Database 相同的引擎上，因此它提供了相同的 T-sql 编程外围应用程序，使开发应用程序或解决方案的开发变得更加简单快捷，同时使应用程序IoT Edge 设备、数据中心和云之间的可移植性。

## <a name="deployment-models"></a>部署模型

Azure Marketplace 提供 azure SQL 数据库边缘，可将其部署为[Azure IoT Edge](../iot-edge/about-iot-edge.md)的模块。 有关详细信息，请参阅[部署 AZURE SQL 数据库边缘](deploy-portal.md)。<br>

![SQL 数据库边缘概述示意图](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL 数据库边缘版本

SQL 数据库边缘可用于三个不同的版本或软件计划。 这些版本具有相同的功能集，并且仅在其使用权限和它们支持的 cpu/内存量方面存在差异。

   |**规划**  |**说明**  |
   |---------|---------|
   |开发人员  |  仅开发 sku，具有如下所述的标准 sku 施加的限制： |
   |标准   |  标准计划支持 SQL 数据库边缘容器最多支持4个 CPU，最多支持 32 GB 的内存。 |
   |高级    |  高级 sku 最多支持8个核心，适用于 SQL 数据库边缘容器最多支持 64 GB 的内存。 |

## <a name="pricing-and-availability"></a>定价和可用性

Azure SQL Database 目前处于预览阶段。 有关定价和可用性的详细信息，请参阅[AZURE SQL 数据库边缘](https://azure.microsoft.com/services/sql-database-edge/)。

> [!IMPORTANT]
> 若要了解 Azure SQL 数据库边缘与 SQL Server 之间的功能差异，以及不同的 Azure SQL 数据库边缘选项之间的差异，请参阅[SQL 数据库边缘数据库功能](https://azure.microsoft.com/services/sql-database-edge/)。

## <a name="streaming-capabilities"></a>流式处理功能  

Azure SQL 数据库边缘提供内置的流式处理功能，可用于实时分析和复杂的事件处理。 流式处理功能是使用与[Azure 流分析](../stream-analytics/stream-analytics-introduction.md)相同的构造构建的，并提供了类似的功能与[Azure IoT Edge 流分析](../stream-analytics/stream-analytics-edge.md)。

适用于 Azure SQL 数据库边缘的流式处理引擎旨在实现低延迟、复原、高效地使用带宽和合规性。

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>机器学习和人工智能功能

Azure SQL 数据库边缘通过集成开放格式 ONNX （开放式神经网络交换）运行时（允许在不同框架之间交换深度学习和神经网络模型）提供内置的机器学习和分析功能。 有关 ONNX 的详细信息，请参阅[此处](https://onnx.ai/)。 使用 ONNX 运行时，可以灵活地使用所选的语言或工具开发模型，然后将其转换为 ONNX 格式以便在 SQL 数据库边缘内执行。 有关详细信息，请参阅[SQL 数据库边缘中的机器学习和](onnx-overview.md)适用于 ONNX 的人工智能。

## <a name="working-with-azure-sql-database-edge"></a>使用 Azure SQL 数据库边缘

通过 Azure SQL 数据库边缘，开发和维护应用程序的效率更高且效率更高。 用户可以使用所有熟悉的工具和技能为其 IoT Edge 需求构建强大的应用程序和解决方案。 用户可以使用如下工具在 SQL 数据库边缘中进行开发：

- [Azure 门户](https://portal.azure.com/)-一种用于管理所有 Azure 服务的基于 web 的应用程序。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) -用于管理任何 sql 基础结构的免费可下载客户端应用程序，从 SQL SERVER 到 SQL 数据库。
- [Visual Studio 中的 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt/) -用于开发 SQL Server 关系数据库、SQL 数据库、Integration Services 包、Analysis Services 数据模型和 Reporting Services 报表的免费可下载客户端应用程序。
- [Azure Data Studio](/sql/azure-data-studio/what-is/) -适用于数据专业人员的免费、可下载、跨平台数据库工具，适用于在 Windows、MacOS 和 Linux 上使用 Microsoft 系列本地和云数据平台。
- [Visual Studio Code](https://code.visualstudio.com/docs) -适用于 Windows、MacOS 和 Linux 的免费可下载的开源代码编辑器。 它支持扩展，其中包括用于查询 Microsoft SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库的[mssql 扩展](https://aka.ms/mssql-marketplace)。


## <a name="next-steps"></a>后续步骤

- 有关定价和可用性相关的详细信息，请参阅[AZURE SQL 数据库边缘](https://azure.microsoft.com/services/sql-database-edge/)。
- 为订阅启用 Azure SQL 数据库边缘的请求。
- 若要开始操作，请参阅以下内容：
  - [通过 Azure 门户部署 SQL 数据库边缘](deploy-portal.md)
  - [SQL 数据库边缘的机器学习和人工智能](onnx-overview.md)
