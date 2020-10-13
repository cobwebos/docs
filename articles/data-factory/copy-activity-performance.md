---
title: 复制活动性能和可伸缩性指南
description: 了解使用复制活动时影响 Azure 数据工厂中数据移动性能的关键因素。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: a6e89883ec0e1e493bad9572876af86f4a0d3853
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324425"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>复制活动性能和可伸缩性指南

> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-copy-activity-performance.md)
> * [当前版本](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

有时，你想要执行从 data lake 或企业数据仓库 (EDW) 到 Azure 的大规模数据迁移。 在其他情况下，需要将大量数据从不同的源引入 Azure，用于大数据分析。 在每种情况下，实现最佳性能和可伸缩性至关重要。

Azure 数据工厂 (ADF) 提供一种用于引入数据的机制。 ADF 具有以下优势：

* 处理大量数据
* 高性能
* 经济高效

这些优势使 ADF 成为需要构建高性能的可缩放数据引入管道的数据工程师。

阅读本文后，能够回答以下问题：

* 对于数据迁移和数据引入方案，使用 ADF 复制活动可以实现哪种程度的性能和可伸缩性？
* 应执行哪些步骤来优化 ADF 复制活动的性能？
* 可以利用哪些 ADF 性能优化设置来优化单个复制活动运行的性能？
* 优化复制性能时，需要考虑 ADF 以外的其他哪些因素？

> [!NOTE]
> 如果你对常规复制活动不熟悉，在阅读本文前请参阅[复制活动概述](copy-activity-overview.md)。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>使用 ADF 可实现的复制性能和可伸缩性

ADF 提供的无服务器体系结构允许不同级别的并行性。

此体系结构允许开发最大程度地提高环境数据移动吞吐量的管道。 这些管道充分利用以下资源：

* 网络带宽
* 每秒存储输入/输出操作数 (IOPS) 和带宽

此完全使用量意味着可以通过测量以下资源提供的最小吞吐量来估算总体吞吐量：

* 源数据存储
* 目标数据存储
* 源和目标数据存储之间的网络带宽

下表计算复制持续时间。 持续时间取决于数据大小和环境的带宽限制。

&nbsp;

| 数据大小/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分钟    | 1.4 分钟   | 0.3 分钟   | 0.1 分钟  | 0.03 分钟 | 0.01 分钟 | 0.0 分钟   |
| **10 GB**                   | 27.3 分钟   | 13.7 分钟  | 2.7 分钟   | 1.3 分钟  | 0.3 分钟  | 0.1 分钟  | 0.03 分钟  |
| **100 GB**                  | 4.6 小时    | 2.3 小时   | 0.5 小时   | 0.2 小时  | 0.05 小时 | 0.02 小时 | 0.0 小时   |
| **1 TB**                    | 46.6 小时   | 23.3 小时  | 4.7 小时   | 2.3 小时  | 0.5 小时  | 0.2 小时  | 0.05 小时  |
| **10 TB**                   | 19.4 天  | 9.7 天  | 1.9 天  | 0.9 天 | 0.2 天 | 0.1 天 | 0.02 天 |
| **100 TB**                  | 194.2 天 | 97.1 天 | 19.4 天 | 9.7 天 | 1.9 天 | 1 天    | 0.2 天  |
| **1 PB**                    | 64.7 个月    | 32.4 个月   | 6.5 个月    | 3.2 个月   | 0.6 个月   | 0.3 个月   | 0.06 个月   |
| **10 PB**                   | 647.3 个月   | 323.6 个月  | 64.7 个月   | 31.6 个月  | 6.5 个月   | 3.2 个月   | 0.6 个月    |
| | |  | | |  | | |

ADF 副本可在不同的级别缩放：

![ADF 副本的缩放方式](media/copy-activity-performance/adf-copy-scalability.png)

* ADF 控制流可以并行启动多个复制活动（例如，使用 [For Each 循环](control-flow-for-each-activity.md)）。

* 单个复制活动可以利用可缩放的计算资源。
  * 使用 Azure 集成运行时 (IR) 时，最多可以为每个复制活动 [ (DIUs) 指定256数据集成单元 ](#data-integration-units) ，以无服务器方式。
  * 使用自承载 IR 时，可以采用以下方法之一：
    * 手动纵向扩展计算机。
    * 横向扩展到多台计算机， (多 [达4个节点](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) ，一个复制活动将跨所有节点对其文件集进行分区。

* 单个复制活动[并行](#parallel-copy)使用多个线程读取和写入数据存储。

## <a name="performance-tuning-steps"></a>性能优化步骤

执行以下步骤，使用复制活动来优化 Azure 数据工厂服务的性能：

1. **选取测试数据集并建立基线。**

    在开发过程中，请对具有代表性的数据示例使用复制活动来测试管道。 你选择的数据集应按以下属性表示典型的数据模式：

    * 文件夹结构
    * 文件模式
    * 数据架构

    您的数据集应该足够大，以便评估复制性能。 若要完成复制活动，大小良好至少需要10分钟。 收集 [复制活动监视](copy-activity-monitoring.md)后的执行详细信息和性能特征。

2. **如何最大化单个复制活动的性能**：

    建议使用单个复制活动最大限度地提高性能。

    * **如果正在 _Azure_ integration runtime 上执行复制活动：**

        一开始对[数据集成单位 (DIU)](#data-integration-units) 和[并行复制](#parallel-copy)设置使用默认值。

    * **如果在 _自承载_ 集成运行时上执行复制活动：**

        建议使用专用计算机托管 IR。 计算机应该与托管数据存储的服务器分离。 一开始对[并行复制](#parallel-copy)设置使用默认值，并对自承载 IR 使用单个节点。

    执行性能测试运行。 记下实现的性能。 包括使用的实际值，如 DIUs 和并行副本。 有关如何收集运行结果和所用性能设置，请参阅[复制活动监视](copy-activity-monitoring.md)。 了解如何对 [复制活动性能进行故障排除](copy-activity-performance-troubleshooting.md) ，确定并解决瓶颈问题。

    按照故障排除和优化指南进行迭代，执行其他性能测试。 一旦单个复制活动运行无法获得更好的吞吐量，请考虑是否通过同时运行多个副本来最大程度地提高聚合吞吐量。 下一编号项目中讨论了此选项。

3. **如何通过并行运行多项复制来最大化聚合吞吐量：**

    至此，你已将单个复制活动的性能最大化。 如果尚未获得环境上限，可以并行运行多个复制活动。 可以使用 ADF 控制流构造并行运行。 这种构造是 [每个循环](control-flow-for-each-activity.md)的。 有关详细信息，请参阅以下有关解决方案模板的文章：

    * [从多个容器复制文件](solution-template-copy-files-multiple-containers.md)
    * [将数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)
    * [使用控制表进行大容量复制](solution-template-bulk-copy-with-control-table.md)

4. **将配置扩展至整个数据集。**

    对执行结果和性能满意时，可以扩展定义和管道以覆盖整个数据集。

## <a name="troubleshoot-copy-activity-performance"></a>排查复制活动的性能问题

遵循[性能优化步骤](#performance-tuning-steps)为方案规划并执行性能测试。 了解如何对 Azure 数据工厂中的每个复制活动运行性能问题进行故障排除，以 [解决复制活动性能](copy-activity-performance-troubleshooting.md)问题。

## <a name="copy-performance-optimization-features"></a>复制性能优化功能

Azure 数据工厂提供以下性能优化功能：

* [数据集成单元](#data-integration-units)
* [自承载集成运行时可伸缩性](#self-hosted-integration-runtime-scalability)
* [并行复制](#parallel-copy)
* [暂存复制](#staged-copy)

### <a name="data-integration-units"></a>数据集成单元

 (DIU) 的数据集成单元是表示 Azure 数据工厂中单个单元的强大功能的度量值。 幂是 CPU、内存和网络资源分配的组合。 DIU 仅适用于 [Azure 集成运行时](concepts-integration-runtime.md#azure-integration-runtime)。 DIU 不适用于 [自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)。 [在此处了解详细信息](copy-activity-performance-features.md#data-integration-units)。

### <a name="self-hosted-integration-runtime-scalability"></a>自承载集成运行时可伸缩性

你可能希望托管不断增长的并发工作负荷。 或者，您可能想要在当前工作负荷级别获得更高的性能。 可以通过以下方法增强处理规模：

* 可以通过增加_up_可在节点上运行的[并发作业](create-self-hosted-integration-runtime.md#scale-up)的数目，增加自承载 IR。  
仅当节点的处理器和内存小于完全使用时，才可以进行扩展。
* 可以通过添加更多节点 (机) _来横向扩展_ 自承载 IR。

有关详细信息，请参阅：

* [复制活动性能优化功能：自承载集成运行时可伸缩性](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [创建和配置自承载集成运行时：缩放注意事项](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>并行复制

可以设置 `parallelCopies` 属性以指示要复制活动使用的并行度。 将此属性视为复制活动中的最大线程数。 线程并行操作。 线程从源读取，或写入接收器数据存储。 [了解详细信息](copy-activity-performance-features.md#parallel-copy)。

### <a name="staged-copy"></a>暂存复制

数据复制操作可以将数据 _直接_ 发送到接收器数据存储。 或者，你可以选择使用 Blob 存储作为 _临时过渡_ 存储。 [了解详细信息](copy-activity-performance-features.md#staged-copy)。

## <a name="next-steps"></a>后续步骤

请参阅其他复制活动文章：

* [复制活动概述](copy-activity-overview.md)
* [排查复制活动的性能问题](copy-activity-performance-troubleshooting.md)
* [复制活动性能优化功能](copy-activity-performance-features.md)
* [使用 Azure 数据工厂将数据从 Data Lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
* [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)
