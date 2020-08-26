---
title: 教程：Azure Synapse Analytics 入门
description: 本教程将介绍设置和使用 Azure Synapse Analytics 的基本步骤。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075865"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics 入门

本教程分步介绍了 Azure Synapse Analytics 的主要功能领域。 对于那些想要了解 Azure Synapse Analytics 的关键场景的用户而言，本教程是理想的入门选择。 按照本教程中的步骤操作后，你将拥有一个功能齐全的 Synapse 工作区，可在其中开始使用 SQL、按需 SQL 和 Apache Spark 来分析数据。

你将学习以下内容：
* 在 Azure 订阅中预配 Synapse 工作区
* 配置对 ADLSGEN2 帐户的访问控制，使其与 Synapse 工作区无缝协作
* 将 NYCTaxi 示例数据加载到 Synapse 工作区，以便 SQL、按需 SQL 和 Spark 可以使用这些数据
* 使用 Synapse Studio 编辑并运行 SQL 脚本和 Synapse 笔记本
* 查询 SQL 表和 Spark 表
* 将数据从 SQL 表加载到 Spark 数据帧
* 将数据从 Spark 数据帧加载到 SQL 表
* 浏览 ADLSGEN2 帐户的内容
* 使用 Spark 和按需 SQL 分析 ADLSGEN2 帐户中的 parquet 数据文件 
* 生成一个数据管道以每小时自动运行 Synapse 笔记本

按照如下所示的顺序执行步骤，你将浏览诸多功能并了解如何运用其核心功能。

* [第 1 步 - 创建并设置 Synapse 工作区](get-started-create-workspace.md)
* [第 2 步 - 使用 SQL 池进行分析](get-started-analyze-sql-pool.md)
* [第 3 步 - 使用 Spark 进行分析](get-started-analyze-spark.md)
* [第 4 步 - 使用按需 SQL 进行分析](get-started-analyze-sql-on-demand.md)
* [第 5 步 - 分析存储帐户中的数据](get-started-analyze-storage.md)
* [第 6 步 - 通过管道进行编排](get-started-pipelines.md)
* [第 7 步 - 使用 Power BI 可视化数据](get-started-visualize-power-bi.md)
