---
title: 流分析简介 | Microsoft Docs
description: 了解流分析，这是一种托管的服务，可以帮助你分析物联网 (IoT) 实时提供的流式数据。
keywords: 分析即服务、托管服务、流处理、流式分析、什么是流分析
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/17/2017
ms.author: jeanb
ms.openlocfilehash: 70a4b0b358b79ec892483a9fa5573e4bf2d57534
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="what-is-stream-analytics"></a>什么是流分析？

Azure 流分析是托管的事件处理引擎，用于设置针对流式处理数据的实时分析计算。 数据可能来自设备、传感器、网站、社交媒体源、应用程序、基础结构系统等。 

可以使用流分析检查来自设备或进程的大量数据流，从数据流提取信息，以及确定模式、趋势和关系。 可以使用这些模式来触发其他进程或操作（例如警报、自动化工作流）、将信息馈送到报告工具，或者将其存储起来供以后调查。 

下面是一些示例：

* 股票交易分析和警报。
* 欺诈行为检测、数据和标识保护。 
* 嵌入式传感器和传动器分析。
* Web 点击流分析。

## <a name="how-does-stream-analytics-work"></a>流分析工作原理

下图绘制的是流分析管道，说明了如何引入和分析数据，然后发送数据进行演示或操作。 

![流分析管道](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

流分析一开始需要流式处理数据源。 可以使用 Azure 事件中心或 IoT 中心将数据从设备引入 Azure。 也可从数据存储（例如 Azure Blob 存储）拉取数据。 

若要检查流，可创建一项流分析作业，以便指定数据的来源。 该作业还指定了转换；如何查找数据、模式或关系。 就此任务来说，流分析支持类似 SQL 的查询语言，用于对某个时段的流式处理数据进行筛选、排序、聚合和联接。

最后，此作业为该转换数据指定一个输出。 可以针对所分析的信息来控制后续操作。 例如，可以针对分析执行以下操作：

* 发送更改设备设置的命令。 
* 将数据发送到受监视的队列，以便根据发现执行进一步的操作。 
* 将数据发送到 Power BI 仪表板。
* 将数据发送到 Data Lake Store、Azure SQL 数据库或 Azure Blob 存储之类的存储。

可以调整在作业运行时每秒处理的事件数。 也可以生成进行故障排除所需的诊断日志。

## <a name="key-capabilities-and-benefits"></a>主要功能和优点

流分析经过专门的设计，具有易用、灵活的特点，并可根据作业大小进行缩放。

### <a name="connect-inputs-and-outputs"></a>连接输入和输出

流分析可直接连接到 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)和 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)进行流引入，并可连接到 [Azure Blob 存储服务](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts)进行历史数据引入。 组合使用从事件中心获取的数据、流分析以及其他数据源和处理引擎。 作业输入也可包括引用数据（静态数据或缓慢变化的数据）。 可以通过将流式处理数据联接到该引用数据来执行查找操作，其方式与数据库查询方式相同。

在多个方向路由流分析作业输出。 写入到存储，例如 Azure Blob、Azure SQL 数据库、Azure Data Lake Store 或 Azure Cosmos DB。 可以在这里通过 Azure HDInsight 运行批处理分析。 也可以将输出发送到供其他进程使用的其他服务（例如事件中心、Azure 服务总线、队列），或者发送到 Power BI 进行可视化。

### <a name="simple-to-use"></a>易于使用

若要定义转换，可使用简单的声明性[流分析查询语言](https://msdn.microsoft.com/library/azure/dn834998.aspx)，不需编程即可进行复杂的分析。 查询语言采用流式处理数据作为其输入。 然后，你可以对数据进行筛选和排序、对值进行聚合、执行计算、对数据进行联接（在流内进行，或者以引用数据作为联接目标），以及使用地理空间函数。 可以在门户中使用 IntelliSense 和语法检查来编辑查询，还可以使用可从实时流中提取的示例数据来测试查询。

### <a name="extensible-query-language"></a>可扩展查询语言

可以通过定义和调用其他函数来扩展查询语言的功能。 可以在 Azure 机器学习服务中定义函数调用，以便利用 Azure 机器学习解决方案。 也可集成 JavaScript 用户定义函数 (UDF)，以便在流分析查询过程中执行复杂的计算。

### <a name="scalable"></a>可缩放

流分析每秒可处理多达 1 GB 的传入数据。 与 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)和 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)集成后，即可通过作业每秒引入数百万个来自已连接设备、点击流、日志文件等的事件。 使用事件中心的分区功能，可以将计算分成多个逻辑步骤，每个步骤还可以进一步细分，以提高可伸缩性。

### <a name="low-cost"></a>低成本

流分析已作为云服务进行成本优化。 根据流单元使用量和处理的数据量付款。 使用情况取决于已处理事件的数量，以及在作业群集中预配的计算能力。

### <a name="reliable"></a>可靠

作为托管服务，流分析有助于防止数据丢失，确保业务连续性。 如果发生故障，该服务会提供内置的恢复功能。 由于能够在内部维持相关状态，因此服务提供的可重复结果确保可以对事件进行存档并在未来重新处理，始终获得相同的结果。 因此，你可以在进行根本原因分析、假设情况分析等操作时，及时回过头去调查计算结果。

## <a name="next-steps"></a>后续步骤

* 入门方式：[在 IoT 设备中试验输入和查询](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md)。
* 构建[端到端流分析解决方案](stream-analytics-real-time-fraud-detection.md)，通过检查电话元数据来找出欺诈性电话。
* 在 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)中查找流分析问题的答案。

