---
title: 复制活动性能和可伸缩性指南
description: 了解在使用复制活动时影响 Azure 数据工厂中数据移动性能的关键因素。
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
ms.date: 03/11/2020
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261393"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>复制活动性能和可伸缩性指南

> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-copy-activity-performance.md)
> * [当前版本](copy-activity-performance.md)

无论你是要执行从 data lake 或企业数据仓库（EDW）到 Azure 的大规模数据迁移，还是想要将来自不同源的大规模数据迁移到 azure 以进行大数据分析，实现最佳性能和能力.  Azure 数据工厂提供了一种高性能、可复原且经济高效的机制，用于大规模引入数据，使其适合于希望构建高性能和可缩放的数据引入管道的数据工程师。

阅读本文后，能够回答以下问题：

- 对于数据迁移和数据引入方案，可以使用 ADF 复制活动实现哪些级别的性能和可伸缩性？

- 我应该采取哪些步骤来优化 ADF 复制活动的性能？
- 我可以利用什么 ADF 性能优化旋钮来优化单次复制活动运行的性能？
- 优化复制性能时，应考虑 ADF 以外的其他因素？

> [!NOTE]
> 如果你不熟悉一般的复制活动，请在阅读本文之前参阅[复制活动概述](copy-activity-overview.md)。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>使用 ADF 可实现复制性能和可伸缩性

ADF 提供的无服务器体系结构允许不同级别的并行度，这允许开发人员构建管道来充分利用网络带宽以及存储 IOPS 和带宽，最大程度地提高环境的数据移动吞吐量。  这意味着，可以通过度量源数据存储中提供的最小吞吐量、目标数据存储以及源和目标之间的网络带宽，来估计可以实现的吞吐量。  下表根据数据大小和环境的带宽限制来计算复制持续时间。 

| 数据大小/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分钟    | 1.4 分钟   | 0.3 分钟   | 0.1 分钟  | 0.03 分钟 | 0.01 分钟 | 0.0 分钟   |
| **10 GB**                   | 27.3 分钟   | 13.7 分钟  | 2.7 分钟   | 1.3 分钟  | 0.3 分钟  | 0.1 分钟  | 0.03 分钟  |
| **100 GB**                  | 4.6 小时    | 2.3 小时   | 0.5 小时   | 0.2 小时  | 0.05 小时 | 0.02 小时 | 0.0 小时   |
| **1 TB**                    | 46.6 小时   | 23.3 小时  | 4.7 小时   | 2.3 小时  | 0.5 小时  | 0.2 小时  | 0.05 小时  |
| **10 TB**                   | 19.4 天  | 9.7 天  | 1.9 天  | 0.9 天 | 0.2 天 | 0.1 天 | 0.02 天 |
| **100 TB**                  | 194.2 天 | 97.1 天 | 19.4 天 | 9.7 天 | 1.9 天 | 1 天    | 0.2 天  |
| **1 PB**                    | 64.7 mo    | 32.4 mo   | 6.5 mo    | 3.2 mo   | 0.6 mo   | 0.3 mo   | 0.06 mo   |
| **10 PB**                   | 647.3 mo   | 323.6 mo  | 64.7 mo   | 31.6 mo  | 6.5 mo   | 3.2 mo   | 0.6 mo    |

ADF 副本可在不同级别进行缩放：

![ADF 复制的缩放方式](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 控制流可以并行启动多个复制活动，例如，[为每个循环](control-flow-for-each-activity.md)使用。
- 单个复制活动可以利用可缩放的计算资源：使用 Azure Integration Runtime 时，可以以无服务器方式为每个复制活动指定[最多 256 DIUs](#data-integration-units) ;使用自承载 Integration Runtime 时，可以手动纵向扩展计算机或向外扩展到多台计算机（[最多4个节点](create-self-hosted-integration-runtime.md#high-availability-and-scalability)），单个复制活动会将其文件集分区在所有节点上。
- 单个复制活动将使用多个线程[并行](#parallel-copy)读取和写入数据存储区。

## <a name="performance-tuning-steps"></a>性能优化步骤

执行以下步骤可使用复制活动来优化 Azure 数据工厂服务的性能。

1. **选取测试数据集并建立基线。** 在开发阶段，通过对代表性数据示例使用复制活动来测试管道。 你选择的数据集应表示典型的数据模式（文件夹结构、文件模式、数据架构等），并且足够大以评估复制性能，例如，复制活动需要10分钟或更长时间才能完成。 收集[复制活动监视](copy-activity-monitoring.md)后的执行详细信息和性能特征。

2. **如何最大程度地提高单个复制活动的性能**：

   首先，建议使用单个复制活动最大限度地提高性能。

   - **如果复制活动正在 Azure Integration Runtime 上执行：** 以[数据集成单元（DIU）](#data-integration-units)的默认值和[并行复制](#parallel-copy)设置开始。 

   - **如果复制活动是在自承载 Integration Runtime 上执行的：** 我们建议你使用独立于托管数据存储的服务器的专用计算机来承载集成运行时。 从[并行复制](#parallel-copy)设置的默认值开始，对自承载 IR 使用单个节点。  

   执行性能测试运行，并记下实现的性能以及使用的实际值，如 DIUs 和并行副本。 请参阅[复制活动监视](copy-activity-monitoring.md)，了解如何收集所使用的运行结果和性能设置，并了解如何对[复制活动性能进行故障排除](copy-activity-performance-troubleshooting.md)，确定并解决瓶颈问题。 

   按照故障排除和优化指南进行迭代，执行其他性能测试。 一旦单个复制活动运行无法获得更好的吞吐量，请考虑通过运行同时引用步骤3的多个副本来最大程度地提高聚合吞吐量。


3. **如何通过并行运行多个副本来最大程度地提高聚合吞吐量：**

   现在，你已最大程度地提高了单个复制活动的性能，但如果尚未达到环境的吞吐量上限–网络、源数据存储和目标数据存储，则可以使用 ADF 控制流构造（如[For each 循环](control-flow-for-each-activity.md)）并行运行多个复制活动。 请参阅[从多个容器复制文件、将](solution-template-copy-files-multiple-containers.md)[数据从 Amazon S3 迁移到 ADLS Gen2](solution-template-migration-s3-azure.md)或[使用控制表解决方案模板进行大容量复制](solution-template-bulk-copy-with-control-table.md)（如一般示例）。

5. **将配置扩展到整个数据集。** 对执行结果和性能满意后，可以扩展定义和管道，以覆盖整个数据集。

## <a name="troubleshoot-copy-activity-performance"></a>复制活动性能疑难解答

按照[性能优化步骤](#performance-tuning-steps)来规划和执行适用于你的方案的性能测试。 了解如何对 Azure 数据工厂中的每个复制活动运行性能问题进行故障排除，以[解决复制活动性能](copy-activity-performance-troubleshooting.md)问题。

## <a name="copy-performance-optimization-features"></a>复制性能优化功能

Azure 数据工厂提供以下性能优化功能：

- [数据集成单元](#data-integration-units)
- [自承载集成运行时可伸缩性](#self-hosted-integration-runtime-scalability)
- [并行复制](#parallel-copy)
- [暂存复制](#staged-copy)

### <a name="data-integration-units"></a>数据集成单元

数据集成单元是表示 Azure 数据工厂中单个单元的幂（CPU、内存和网络资源分配的组合）的度量值。 数据集成单元仅适用于[Azure 集成运行](concepts-integration-runtime.md#azure-integration-runtime)时，但不适用于[自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)。 [了解详细信息](copy-activity-performance-features.md#data-integration-units)。

### <a name="self-hosted-integration-runtime-scalability"></a>自承载集成运行时可伸缩性

若要托管不断增加的并发工作负荷或实现更高的性能，可以增加或横向扩展自承载 Integration Runtime。 [了解详细信息](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)。

### <a name="parallel-copy"></a>并行复制

你可以设置并行复制，以指示你希望复制活动使用的并行度。 可以将此属性视为复制活动中的最大线程数，这些线程从源读取数据或将数据写入接收器数据存储。 [了解详细信息](copy-activity-performance-features.md#parallel-copy)。

### <a name="staged-copy"></a>暂存复制

将数据从源数据存储复制到接收器数据存储时，可能会选择使用 Blob 存储作为过渡暂存存储。 [了解详细信息](copy-activity-performance-features.md#staged-copy)。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能疑难解答](copy-activity-performance-troubleshooting.md)
- [复制活动性能优化功能](copy-activity-performance-features.md)
- [使用 Azure 数据工厂将数据从 data lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)
