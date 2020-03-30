---
title: 使用 Azure 宇宙数据库容量规划器估计成本
description: Azure Cosmos DB 容量规划器允许您估计工作负荷所需的吞吐量 （RU/s） 和成本。 本文介绍如何使用新版本的容量规划器来估计所需的吞吐量和成本。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707625"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>使用 Azure 宇宙数据库容量规划器估计 RU/s

为工作负荷配置 Azure Cosmos 数据库和容器，其预配吞吐量或[请求单位 （RU/s）](request-units.md)对于优化成本和性能至关重要。 本文介绍如何使用 Azure Cosmos DB[容量规划器](https://cosmos.azure.com/capacitycalculator/)来估算所需的 RU/s 和工作负荷成本。 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>如何使用 Azure Cosmos DB 容量规划器估计吞吐量和成本

容量规划器可在两种模式下使用。

|**模式**  |**说明**  |
|---------|---------|
|Basic|提供快速、高级 RU/s 和成本估算。 此模式假定默认的 Azure Cosmos DB 设置用于索引策略、一致性和其他参数。 <br/><br/>在评估要在 Azure Cosmos DB 上运行的潜在工作负荷时，使用基本模式进行快速的高级估计。|
|高级|提供更详细的 RU/s 和成本估算，并能够调整其他设置 — 索引策略、一致性级别以及影响成本和吞吐量的其他参数。 <br/><br/>在估计新项目的 RU/s 或想要更详细的估计时，请使用高级模式。 |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>使用基本模式估计预配吞吐量和成本
要使用基本模式快速估计工作负荷，请导航到[容量规划器](https://cosmos.azure.com/capacitycalculator/)。 根据工作负荷输入以下参数： 

|**输入**  |**说明**  |
|---------|---------|
|区域数|Azure Cosmos DB 面向所有 Azure 区域提供。 选择工作负荷所需的区域数。 您可以将任意数量的区域与 Cosmos 帐户相关联。 有关详细信息，请参阅 Azure Cosmos DB 中的[全局分发](distribute-data-globally.md)。|
|多区域写入|如果启用[多区域写入](distribute-data-globally.md#key-benefits-of-global-distribution)，则应用程序可以读取和写入任何 Azure 区域。 如果禁用多区域写入，应用程序可以将数据写入单个区域。 <br/><br/> 如果希望具有活动-活动工作负载，需要不同区域的低延迟写入，则启用多区域写入。 例如，IOT 工作负载，用于在不同区域以高容量将数据写入数据库。 <br/><br/> 多区域写入保证 99.999% 的读写可用性。 与单个写入区域相比，多区域写入需要更多的吞吐量。 要了解更多信息，请参阅[单行和多写区域文章的 RUS 是如何不同的](optimize-cost-regions.md)。|
|存储的数据总数（每个区域）|以 GB 存储在单个区域中的估计数据总数。|
|项大小|数据项的估计大小（例如文档），范围从 1 KB 到 2 MB。 |
|每个区域的读取/秒|每秒预期读取数。 |
|每个区域的写入/秒|每秒预期写入数。 |

填写所需详细信息后，选择 **"计算**"。 "**成本估算**"选项卡显示存储和预配吞吐量的总成本。 您可以展开此选项卡中的 **"显示详细信息"** 链接，以获取有关读取和写入请求所需的吞吐量的细分。 每次更改任何字段的值时，选择 **"计算"** 以重新计算估计成本。 

![容量规划器基本模式](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>使用高级模式估计预配吞吐量和成本

高级模式允许您提供更多影响 RU/s 估计的设置。 要使用此选项，请导航到[容量规划器](https://cosmos.azure.com/capacitycalculator/)并使用用于 Azure 的帐户登录到该工具。 登录选项在右侧角可用。 

登录后，您可以看到与基本模式下的字段相比的其他字段。 根据工作负荷输入其他参数。 

|**输入**  |**说明**  |
|---------|---------|
|API|Azure Cosmos DB 是一种多模型和多 API 服务。 对于新的工作负载，请选择 SQL（核心）API。 |
|区域数|Azure Cosmos DB 面向所有 Azure 区域提供。 选择工作负荷所需的区域数。 您可以将任意数量的区域与 Cosmos 帐户相关联。 有关详细信息，请参阅 Azure Cosmos DB 中的[全局分发](distribute-data-globally.md)。|
|多区域写入|如果启用[多区域写入](distribute-data-globally.md#key-benefits-of-global-distribution)，则应用程序可以读取和写入任何 Azure 区域。 如果禁用多区域写入，应用程序可以将数据写入单个区域。 <br/><br/> 如果希望具有活动-活动工作负载，需要不同区域的低延迟写入，则启用多区域写入。 例如，IOT 工作负载，用于在不同区域以高容量将数据写入数据库。 <br/><br/> 多区域写入保证 99.999% 的读写可用性。 与单个写入区域相比，多区域写入需要更多的吞吐量。 要了解更多信息，请参阅[单行和多写区域文章的 RUS 是如何不同的](optimize-cost-regions.md)。|
|默认一致性|Azure Cosmos DB 支持 5 个一致性级别，允许开发人员平衡一致性、可用性和延迟权衡之间的权衡。 要了解更多信息，请参阅[一致性级别](consistency-levels.md)一文。 <br/><br/> 默认情况下，Azure Cosmos DB 使用会话一致性，这保证了在会话中读取自己的写入功能。 <br/><br/> 与会话、一致前缀和最终一致性相比，选择强或有限过时需要读取所需的 RU/s 的两倍。 不支持与多区域写入的强一致性，并且将自动默认为具有强一致性的单区域写入。 |
|索引编制策略|默认情况下，Azure Cosmos DB[为所有项中的所有属性编制索引](index-policy.md)，以便进行灵活有效的查询（映射到**自动**索引策略）。 <br/><br/> 如果选择**关闭**，则不会对任何属性进行索引。 这将导致写入的 RU 费用最低。 如果希望只执行[点读取](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)（键值查找）和/或写入，并且没有查询，请选择 **"关闭**策略"。 <br/><br/> 自定义索引策略允许您从索引中包括或排除特定属性，以降低写入吞吐量和存储。 要了解更多信息，请参阅[索引策略](index-overview.md)和[索引策略](how-to-manage-indexing-policy.md#indexing-policy-examples)示例文章。|
|存储的数据总数（每个区域）|以 GB 存储在单个区域中的估计数据总数。|
|工作负载模式|如果工作负荷量保持不变，请选择 **"稳定**"。 <br/><br/> 如果工作负荷量随时间而变化，请选择 **"变量**"。  例如，在特定一天或一个月内。 <br/><br/> 如果选择可变工作负载选项，可以使用以下设置：<ul><li>峰值时间百分比：工作负荷需要峰值（最高）吞吐量的月份时间百分比。 <br/><br/> 例如，如果您的工作负载在上午 9 点 - 下午 6 点工作时间内活动频繁，则高峰时段的百分比为：高峰时段为 45 小时/730 小时/月 = +6%。<br/><br/></li><li>峰值时每个区域的读取/秒 - 峰值时预计每秒读取数。</li><li>峰值时每个区域的写入/秒 = 峰值时预计每秒写入数。</li><li>每个区域的读取/秒关闭峰值 = 在非峰值期间预计每秒读取数。</li><li>每个区域的写入/秒关闭峰值 = 在非峰值期间预期每秒写入数。</li></ul>使用高峰和非高峰间隔，您可以通过[以编程方式相应地向上和向下扩展预配吞吐量](set-throughput.md#update-throughput-on-a-database-or-a-container)来优化成本。|
|项大小|数据项的大小（例如文档），范围从 1 KB 到 2 MB。 <br/><br/>您还可以**上传示例 （JSON）** 文档，以便进行更准确的估算。<br/><br/>如果工作负荷在同一容器中有多个类型的项目（具有不同的 JSON 内容），则可以上载多个 JSON 文档并获取估计值。 使用 **"添加新项"** 按钮添加多个示例 JSON 文档。|

您还可以使用 **"保存估计"** 按钮下载包含当前估计值的 CSV 文件。 

![容量规划器高级模式](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Azure Cosmos DB 容量规划器中显示的价格是根据吞吐量和存储的公共定价率估算的。 所有价格均以美元显示。 请参阅[Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)，查看按区域查看所有费率。  

## <a name="estimating-throughput-for-queries"></a>估计查询的吞吐量

Azure Cosmos 容量计算器假定点读取（单个项（例如文档、按 ID 和分区键值的读取）和工作负载的写入。 要估计查询所需的吞吐量，请对 Cosmos 容器中的代表性数据集运行查询[，并获取 RU 费用](find-request-unit-charge.md)。 将 RU 费用乘以您预计每秒运行的查询数，以获得所需的总 RU/秒。 

例如，如果工作负荷需要查询，``SELECT * FROM c WHERE c.id = 'Alice'``即每秒运行 100 次，并且查询的 RU 费用为 10 个 RU，则需要 100 个查询 / 秒 = 10 RU / 查询 = 1000 RU/s，以总共为这些请求提供服务。 将这些 RU/s 添加到工作负载中发生的任何读取或写入所需的 RU/s。

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 宇宙 DB 定价模型](how-pricing-works.md)的更多详细信息。
* 创建新的[Cosmos 帐户、数据库和容器](create-cosmosdb-resources-portal.md)。
* 了解如何[优化预配的吞吐量成本](optimize-cost-throughput.md)。
* 了解如何[使用预留容量优化成本](cosmos-db-reserved-capacity.md)。

