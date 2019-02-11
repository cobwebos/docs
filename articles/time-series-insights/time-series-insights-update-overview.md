---
title: 概述：Azure 时序见解预览版 | Microsoft Docs
description: Azure 时序见解预览版概述。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 847eddc78f8abc938e68e6fe383a773dadeaf779
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557829"
---
# <a name="azure-time-series-insights-preview-overview"></a>Azure 时序见解预览版概述

Azure 时序见解预览版是一种端到端的平台即服务产品/服务。 它用于引入、处理和查询高度情景化且时序优化的 IoT 规模的数据。 时序见解是即席数据浏览和运营分析的理想选择。 时序见解是唯一可扩展且自定义的服务产品/服务，满足行业 IoT 部署的广泛需求。

## <a name="video"></a>视频

在本视频中，我们概述了 Azure 时序见解预览版（一种基于云的 IoT 分析平台）。

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>定义 IoT 数据

IoT 数据是在资产密集型组织中提供的“行业”数据。 IoT 数据通常是高度非结构化的，因为它发送自记录相当具有干扰性的度量值的资产。 这些度量值包括温度、运动和湿度。 这些数据流通常带有下述特征：显著性差异、损坏的消息和错误读数。 在开始分析之前，必须对这些流中的数据进行清理。 IoT 数据通常仅在第一方数据源（如 CRM 或 ERP）提供的其他数据输入的上下文中有意义。 输入也可来自第三方数据源，例如天气或位置。

因此，只有一小部分的此类数据用于运营和业务目的。 此类数据提供一致、广泛、最新且正确的信息，适用于业务报告和分析。 将收集的 IoT 数据转换为可操作的见解需要：

* 数据处理，用于数据的清理、筛选、内插、转换和准备操作，以便进行分析。
* 用于导航和了解数据的结构（目的是规范化和情景化数据）。
* 经济有效的存储，适用于长期/无限期保留数十年时间的已处理或衍生的数据和原始数据。

典型的 IoT 数据流如下图所示。

  ![IoT 数据流][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>适用于行业 IoT 的 Azure 时序见解

当前的 IoT 环境很多样。 客户遍及制造业、汽车业、能源业、公用事业、智能建筑和咨询行业。 方案包括即席数据浏览，其中数据的形状未知。 方案还包括通过架构化或显式建模的数据进行运营分析，以提高运营效率。 这些方案通常并行存在，并且支持不同的用例。 对于工业 IoT 企业及其数字革命的成功至关重要的平台功能包括：

- 多层存储，包括冷热存储。 
- 能够存储数十年的时序数据。 
- 能够显式建模和优化基于资产的运营智能的查询。

时序见解是全面的端到端平台即服务产品/服务，用于探索 IoT 数据和获取运营见解。 时序见解提供完全托管的云服务，适用于分析 IoT 规模的时序数据。

可以将原始数据存储在无架构的内存存储中。 然后，可以通过分布式查询引擎和 API 执行交互式即席查询。 利用丰富的用户体验，在几秒钟内可视化数十亿个事件。 详细了解[数据探索功能](./time-series-insights-overview.md)。

时序见解还提供当前为预览版的运营见解功能。 通过结合交互式数据探索和运营智能功能，可以使用时序见解让从 IoT 资产收集的数据发挥出更大的价值。 预览版产品/服务支持：

* 可缩放且性能和成本优化的时序数据存储。 此基于云的 IoT 解决方案可以在数秒内获得多年时序数据的趋势。
* 对于来自资产和设备的派生和非派生信号，语义模型支持描述与其相关联的域和元数据。
* 将基于资产的数据见解与丰富的临时数据分析相结合，增强用户体验。 此组合推动了业务和运营智能。
* 集成了先进的机器学习和分析工具。 工具包括 Azure Databricks、Apache Spark、Azure 机器学习、Jupyter 笔记本和 Power BI。 这些工具可帮助解决时间序列数据挑战并提高运营效率。

运营见解和数据浏览提供了简单的即用即付定价模型，用于数据处理、存储和查询。 此计费模型适合不断变化的业务需求。

此高级数据流关系图显示了更新。

  ![关键功能][2]

由于引入了这些关键的行业 IoT 功能，时序见解具有下述主要优势。

| | |
| ---| ---|
| **适用于 IoT 规模的时序数据的多层存储** | 通过引入数据的通用数据处理管道，可以将数据存储在暖存储中以进行交互式查询。 此外可以将数据存储在冷存储中以存储大量的数据。 利用高性能的基于资产的[查询](./time-series-insights-update-tsq.md)。 |
| **用于情景化原始遥测数据和派生基于资产的见解的时序模型** | 使用描述性的[时序模型](./time-series-insights-update-tsm.md)情景化原始遥测数据。 通过高性能和成本优化的基于设备的查询，派生丰富的运营智能。 |
| **与其他数据解决方案顺利持续集成** |  时序见解中的数据[存储](./time-series-insights-update-storage-ingress.md)在开源的 Apache Parquet 文件中。 无论是第一方还是第三方，这种与其他数据解决方案的集成对于端到端方案都很容易。 这些方案包括商业智能、高级机器学习和预测分析。 |
| **近实时数据浏览** | [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)用户体验提供的可视化功能适用于通过引入管道流式传输的所有数据。 连接事件源后不久，便可查看、浏览和查询事件数据。 通过这种方式，可以验证设备是否按预期方式发出数据。 此外可以监视 IoT 资产的运行状况、生产效率和整体成效。 |
| **根本原因分析和异常情况检测** | [Azure 时序见解预览版资源管理器](./time-series-insights-update-explorer.md)支持的模式和透视视图可以执行和保存多步骤根本原因分析。 你可以将 Azure 流分析与时序见解配合使用，以便通过近实时方式检测警报和异常。 |
| **在时序见解平台上生成的自定义应用程序** | 时序见解支持 [JavaScript SDK](./tutorial-explore-js-client-lib.md)。 SDK 提供了丰富的控件并且简化了对查询的访问。 使用 SDK 基于时序见解生成自定义 IoT 应用程序，以满足特定业务需求。 还可以直接使用时序见解[查询 API](./time-series-insights-update-tsq.md) 将数据推送到自定义 IoT 应用程序中。 |

## <a name="next-steps"></a>后续步骤

开始使用 Azure 时序见解预览版：

> [!div class="nextstepaction"]
> [阅读快速入门指南](./time-series-insights-update-quickstart.md)

了解用例：

> [!div class="nextstepaction"]
> [Azure 时序见解预览版用例](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png