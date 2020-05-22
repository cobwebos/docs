---
title: Azure Synapse Link for Azure Cosmos DB 的实时分析用例
description: 了解如何在供应链分析、预测、报告、实时个性化设置和 IOT 预测性维护中使用 Azure Synapse Link for Azure Cosmos DB。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: f9fafc4b03d5ce18fcfedcffaf2d81f847537865
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596406"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Synapse Link for Azure Cosmos DB：准实时分析用例

适用于 Azure Cosmos DB 的 [Azure Synapse Link](synapse-link.md) 是一种云原生混合事务和分析处理 (HTAP) 功能，可用于对操作数据运行准实时分析。 Synapse Link 在 Azure Cosmos DB 和 Azure Synapse Analytics 之间建立紧密的无缝集成。

你可能会想知道，哪些行业用例可以利用此云原生 HTAP 功能对操作数据进行准实时分析。 下面是 Azure Synapse Link for Azure Cosmos DB 的三个常见用例：

* 供应链分析、预测和报告
* 实时个性化设置
* IOT 方案中的预测性维护，异常情况检测

> [!NOTE]
> Azure Synapse Link for Azure Cosmos DB 面向企业团队想要运行准实时分析的方案。 这些分析是在没有 ETL 的情况下针对操作数据运行的，这些数据是由基于 Azure Cosmos DB 的事务应用程序生成的。 在以下情况下，这并不能取代对单独的数据仓库的需求：当多个数据源中存在传统的数据仓库要求（例如，工作负荷管理、高并发、暂留聚合）时。

## <a name="supply-chain-analytics-forecasting--reporting"></a>供应链分析、预测和报告

研究表明，在供应链操作中嵌入大数据分析会改进订单循环交付时间并提高供应链效率。

制造商载入云原生技术，以打破旧企业资源规划 (ERP) 和供应链管理 (SCM) 系统的约束。 随着供应链每分钟不断生成操作数据（订单、发货、事务数据），制造商需要一个操作数据库。 此操作数据库应进行扩展，以便能够处理数据卷和分析平台，以达到某一级别的实时上下文智能，从而保持领先优势。

以下体系结构展示了将 Azure Cosmos DB 用作供应链分析中的云原生操作数据库和 Synapse Link 的能力：

![供应链分析中的 Azure Synapse Link for Azure Cosmos DB ](./media/synapse-link-use-cases/supply-chain-analytics.png)

根据以前的体系结构，可以通过 Synapse Link for Azure Cosmos DB 实现以下用例：

* 准备和训练预测管道：使用机器学习转换生成有关供应链中的操作数据的见解。 这样，你就可以降低库存、运营成本，并为客户减少订单交付时间。

  通过 Synapse Link，可以在没有任何手动 ETL 过程的情况下分析 Azure Cosmos DB 中不断更改的操作数据。 这节省了额外的成本，并降低了延迟和操作复杂性。 Synapse Link 使数据工程师和数据科学家能够生成可靠的预测管道：

  * 通过利用与 Azure Synapse Analytics 中的 Apache Spark 池的本机集成来查询 Azure Cosmos DB 分析存储中的操作数据。 无需复杂的数据工程即可查询交互式笔记本或计划的远程作业中的数据。

  * 使用 Azure Synapse Analytics 中的 Spark ML 算法和 Azure ML 集成生成机器学习 (ML) 模型。

  * 将模型推理后的结果写回到 Azure Cosmos DB 中以进行准实时操作评分。

* 操作报告：供应链团队需要针对实时、准确的操作数据的灵活且自定义报表。 需要这些报表来获取供应链有效性、盈利和效率的快照视图。 它允许数据分析人员和其他关键利益干系人不断地重新评估业务，并标识要调整的领域以降低运营成本。 

  Synapse Link for Azure Cosmos DB 支持丰富的商业智能 (BI)/报告方案：

  * 通过使用与 Synapse SQL 无服务器的本机集成和 T-SQL 语言的完全表达性来查询 Azure Cosmos DB 分析存储中的操作数据。

  * 借助对熟悉的 BI 工具的 Synapse SQL 无服务器支持，通过 Azure Cosmos DB 建模和发布自动刷新 BI 仪表板。 例如，Azure Analysis Services、Power BI Premium 等。

下面是将批处理数据和流式处理数据集成到 Azure Cosmos DB 的一些指南：

* 批处理数据集成和业务流程：供应链变得越复杂，供应链数据平台就越需要与各种数据源和格式集成。 Azure Synapse 内置了与 Azure 数据工厂相同的数据集成引擎和体验。 此集成使数据工程师无需单独的业务流程引擎即可创建丰富的数据管道：

  * 将数据从 85 个以上的受支持数据源移动到[具有 Azure 数据工厂的 Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md)。

  * 将无代码 ETL 管道写入 Azure Cosmos DB，包括[使用映射数据流的关系到层次结构映射和层次结构到层次结构映射](../data-factory/how-to-sqldb-to-cosmosdb.md)。

* **流式处理数据集成和处理：** 随着行业 IoT（“楼层到商店”中的传感器跟踪资产、已连接的物流群等）的增加，将以流式处理的方式呈爆炸式生成实时数据，该数据需要与传统的缓慢移动的数据集成以生成见解。 Azure 流分析是一种建议流式处理 ETL 以及使用[各种方案](../stream-analytics/streaming-technologies.md)在 Azure 上处理的服务。 Azure 流分析支持[将 Azure Cosmos DB 作为本机数据接收器](../stream-analytics/stream-analytics-documentdb-output.md)。

## <a name="real-time-personalization"></a>实时个性化设置

当前，零售商必须构建安全、可缩放且满足客户和业务需求的电子商务解决方案。 这些电子商务解决方案需要通过自定义的产品和套餐吸引客户，快速且安全地处理事务，将重点放在履约和客户服务上。 通过 Azure Cosmos DB 和最新的 Synapse Link for Azure Cosmos DB，零售商可以实时为客户生成个性化建议。 它们使用低延迟和可优化的一致性设置快速获取见解，如以下体系结构所示：

![实时个性化设置中的 Azure Synapse Link for Azure Cosmos DB](./media/synapse-link-use-cases/real-time-personalization.png)

Synapse Link for Azure Cosmos DB 用例：

* 准备和训练预测管道：你可以使用 Synapse Spark 和机器学习模型跨业务部门或客户细分生成有关操作数据的见解。 这将转换为个性化交付，面向客户细分、预测的最终用户体验和有针对性的营销，以满足最终用户的要求。

## <a name="iot-predictive-maintenance"></a>IOT 预测性维护

行业 IOT 创新大大降低了机器停机时间，并提高了行业所有领域的整体效率。 此类创新之一是云边缘的机器的预测性维护分析。

下面是利用 IoT 预测性维护中的 Azure Synapse Link for Azure Cosmos DB 的云原生 HTAP 功能的体系结构：

![IOT 预测性维护中的 Azure Synapse Link for Azure Cosmos DB](./media/synapse-link-use-cases/iot-predictive-maintenance.png)

Synapse Link for Azure Cosmos DB 用例：

* 准备和训练预测管道：来自 IoT 设备传感器的历史操作数据可用于训练异常检测器等预测模型。 然后，会将这些异常检测器部署回边缘以进行实时监视。 此类良性循环允许持续重新训练预测模型。

* 操作报告：随着数字孪生计划的增加，公司从大量传感器收集大量操作数据，以生成每台计算机的数字副本。 此数据支持 BI 需求，以了解历史数据的趋势以及使用最新热数据的实时应用程序。

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>示例方案：适用于 Azure Cosmos DB 的 HTAP

近十年，大量客户都在使用 Azure Cosmos DB，将其用于需要弹性缩放、统包全球分发、多主数据库复制的任务关键型应用程序，以实现事务工作负荷中读写操作的低延迟和高可用性。
 
以下列表概述了操作数据支持的使用 Azure Cosmos DB 的各种工作负荷模式：

* 实时应用和服务
* 事件流处理
* BI 仪表板
* 大数据分析
* 机器学习

借助 Azure Synapse Link，Azure Cosmos DB 对历史操作数据不仅可以执行事务工作负荷，还可以执行准实时分析工作负荷。 它在没有 ETL 要求的情况下发生，并保证了事务工作负荷的性能隔离。

下图显示了使用 Azure Cosmos DB 的工作负荷模式：![Azure Synapse Link for Azure Cosmos DB 工作负荷模式](./media/synapse-link-use-cases/synapse-link-workload-patterns.png)

让我们以包含跨 20 个国家/地区的全球业务的电子商务公司 CompanyXYZ 为例，说明了选择 Azure Cosmos DB 作为同时满足库存管理平台的事务和分析要求的单一实时数据库的好处。

* CompanyXYZ 的核心业务依赖于库存管理系统 – 因此可用性和可靠性是核心支柱要求。 使用 Azure Cosmos DB 的好处：

  * 凭借与 Azure 基础结构和透明多主数据库全局复制的深度集成，Azure Cosmos DB 可针对区域性服务中断提供行业领先的 [99.999% 的高可用性](high-availability.md)。

* CompanyXYZ 的供应链合作伙伴可能位于不同的地理位置，但他们可能需要查看世界各地的产品库存的单一视图，以支持其本地操作。 这包括需要能够实时读取其他供应链合作伙伴所做的更新。 还能够进行更新，而无需担心与其他合作伙伴在高吞吐量方面发生冲突。 使用 Azure Cosmos DB 的好处：

  * 凭借其独特的多主数据库复制协议和免闩锁且优化了写入的数据库引擎，Azure Cosmos DB 可保证全球任意位置第 99 个百分位的已编入索引的读取和写入延迟均低于 10 毫秒。

  * 通过事务存储中的[实时索引](index-policy.md)，高吞吐量引入批处理和流式处理数据馈送。

  * Azure Cosmos DB 事务存储提供了另外三个选项，而不会走非常一致性和最终一致性这两种极端，从而实现最接近近业务需求的[可用性和性能权衡](consistency-levels-tradeoffs.md)。

* CompanyXYZ 的供应链合作伙伴具有较大变动的流量模式，范围从数百到数百万个请求，因此库存管理平台需要处理流量中的意外情况。  使用 Azure Cosmos DB 的好处：

  * Azure Cosmos DB 的事务存储使用水平分区支持存储和吞吐量的弹性可伸缩性。 在 Autopilot 模式下配置的容器和数据库可以根据应用程序需求自动并即时缩放预配的吞吐量，而不会全局影响工作负荷的可用性、延迟、吞吐量或性能。

* CompanyXYZ 需要建立一个安全分析平台来容纳系统范围内的历史库存数据，以跨供应链合作伙伴、业务部门和功能实现分析和见解。 分析平台需要跨系统、传统的 BI/报告用例、高级分析用例和针对操作库存数据的预测智能解决方案实现协作。 使用 Synapse Link for Azure Cosmos DB 的好处：

  * 通过使用 [Azure Cosmos DB 分析存储](analytical-store-introduction.md)（完全隔离的列存储），Synapse Link 可以在 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 中针对大规模全局分布式操作数据提供无提取-转换-加载 (ETL) 分析。  业务分析师、数据工程师和数据科学家现在可以可互操作的方式使用 Synapse Spark 或 Synapse SQL，来运行准实时商业智能、分析和机器学习管道，而不会影响 Azure Cosmos DB 上的事务工作负荷的性能。 有关更多详细信息，请参阅 [Azure Cosmos DB 中 Synapse Link 的好处](synapse-link.md)。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅下列文档：

* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md) 

* [Azure Cosmos DB 分析存储](analytical-store-introduction.md)

* [使用 Azure Synapse Link for Azure Cosmos DB](configure-synapse-link.md)

* [有关 Azure Synapse Link for Azure Cosmos DB 的常见问题](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics 中的 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)

* [Azure Synapse Analytics 中的 SQL 无服务器/按需版本](../synapse-analytics/sql/on-demand-workspace-overview.md)
