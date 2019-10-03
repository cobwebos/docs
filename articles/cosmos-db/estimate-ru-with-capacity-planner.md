---
title: 使用 Azure Cosmos DB 容量规划器估算成本
description: Azure Cosmos DB 容量规划器允许您估算工作负载所需的吞吐量 (RU/s) 和成本。 本文介绍如何使用新版本的容量规划器来估算所需的吞吐量和成本。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707625"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>使用 Azure Cosmos DB 容量规划器估算 RU/秒

若要对工作负荷进行适当数量的预配吞吐量或[请求单位 (RU/s)](request-units.md)配置 Azure Cosmos 数据库和容器, 则必须优化成本和性能。 本文介绍如何使用 Azure Cosmos DB[容量规划](https://cosmos.azure.com/capacitycalculator/)器来估算所需的 RU/秒和工作负荷的成本。 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>如何 Azure Cosmos DB 容量规划器评估吞吐量和成本

容量规划器可在两种模式下使用。

|**模式**  |**说明**  |
|---------|---------|
|基本|提供快速、高级别的 RU/s 和成本估算。 此模式假设默认 Azure Cosmos DB 设置用于索引策略、一致性和其他参数。 <br/><br/>使用基本模式, 在评估要在 Azure Cosmos DB 上运行的潜在工作负荷时, 可以快速估算。|
|高级|提供了更详细的 RU/s 和成本估算, 还可以调整其他设置-索引策略、一致性级别和影响成本和吞吐量的其他参数。 <br/><br/>在为新项目估计 RU/秒或需要更详细的估计值时, 请使用 "高级" 模式。 |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>使用基本模式估算预配的吞吐量和成本
若要使用基本模式快速估计工作负荷, 请导航到[容量规划](https://cosmos.azure.com/capacitycalculator/)器。 根据工作负荷输入以下参数: 

|**输入**  |**说明**  |
|---------|---------|
|区域数|Azure Cosmos DB 面向所有 Azure 区域提供。 选择工作负荷所需的区域数目。 你可以将任意数量的区域与你的 Cosmos 帐户相关联。 有关更多详细信息, 请参阅 Azure Cosmos DB 中的[全局分发](distribute-data-globally.md)。|
|多区域写入|如果启用[多区域写入](distribute-data-globally.md#key-benefits-of-global-distribution), 你的应用程序可以读取和写入任何 Azure 区域。 如果禁用多区域写入, 你的应用程序可以将数据写入单个区域。 <br/><br/> 如果预计需要在不同区域中进行低延迟写入, 则启用多区域写入。 例如, 将数据写入到不同区域中较大的数据库的 IOT 工作负荷。 <br/><br/> 多区域写入保证读写可用性 99.999%。 与单个写入区域相比, 多区域写入要求更高的吞吐量。 若要了解详细信息, 请参阅[单个和多个写区域一](optimize-cost-regions.md)文中的 ru 的不同之处。|
|存储的总数据 (每个区域)|单个区域中存储的总估计数据 (GB)。|
|项大小|数据项 (如文档) 的估计大小, 范围为 1 KB 到 2 MB。 |
|每个区域的每秒读取数|每秒预期的读取次数。 |
|每个区域写入/秒|预计每秒写入的次数。 |

填写所需的详细信息后, 选择 "**计算**"。 "**成本预估**" 选项卡显示存储和预配吞吐量的总成本。 您可以展开此选项卡中的 "**显示详细信息**" 链接, 以获取读取和写入请求所需的吞吐量细目。 每次更改任何字段的值时, 请选择 "**计算**" 以重新计算预估成本。 

![容量规划器基本模式](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>使用高级模式估算预配的吞吐量和成本

高级模式允许提供更多的设置, 这些设置会影响 RU/秒的估计值。 若要使用此选项, 请导航到[容量规划](https://cosmos.azure.com/capacitycalculator/)器并使用用于 Azure 的帐户登录工具。 "登录" 选项位于右侧。 

登录后, 可以在 "基本" 模式下查看其他字段。 根据工作负荷输入其他参数。 

|**输入**  |**说明**  |
|---------|---------|
|API|Azure Cosmos DB 是一种多模型和多 API 服务。 对于新工作负荷, 请选择 "SQL (内核) API"。 |
|区域数|Azure Cosmos DB 面向所有 Azure 区域提供。 选择工作负荷所需的区域数目。 你可以将任意数量的区域与你的 Cosmos 帐户相关联。 有关更多详细信息, 请参阅 Azure Cosmos DB 中的[全局分发](distribute-data-globally.md)。|
|多区域写入|如果启用[多区域写入](distribute-data-globally.md#key-benefits-of-global-distribution), 你的应用程序可以读取和写入任何 Azure 区域。 如果禁用多区域写入, 你的应用程序可以将数据写入单个区域。 <br/><br/> 如果预计需要在不同区域中进行低延迟写入, 则启用多区域写入。 例如, 将数据写入到不同区域中较大的数据库的 IOT 工作负荷。 <br/><br/> 多区域写入保证读写可用性 99.999%。 与单个写入区域相比, 多区域写入要求更高的吞吐量。 若要了解详细信息, 请参阅[单个和多个写区域一](optimize-cost-regions.md)文中的 ru 的不同之处。|
|默认一致性|Azure Cosmos DB 支持5种一致性级别, 使开发人员能够平衡一致性、可用性和延迟之间的折衷。 若要了解详细信息, 请参阅[一致性级别](consistency-levels.md)一文。 <br/><br/> 默认情况下, Azure Cosmos DB 使用会话一致性, 这可以保证在会话中读取自己的写入操作。 <br/><br/> 与会话、一致前缀和最终一致性相比, 选择 "强" 或 "有限过期" 将需要两个所需的 RU/s。 不支持与多区域写入的强一致性, 它们将自动默认为具有强一致性的单区域写入。 |
|索引编制策略|默认情况下, Azure Cosmos DB 索引所有项中的[所有属性](index-policy.md), 以实现灵活且高效的查询 (映射到**自动**索引策略)。 <br/><br/> 如果选择 "**关闭**", 则不会为任何属性编制索引。 这会导致写入最低的 RU 费用。 如果预计只执行[点读取](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)(键值查找) 和/或写入, 但没有查询, 请选择 "**关闭**策略"。 <br/><br/> 自定义索引策略允许在索引中包括或排除特定属性, 以实现较低的写入吞吐量和存储。 若要了解详细信息, 请参阅[索引策略](index-overview.md)和[示例索引策略](how-to-manage-indexing-policy.md#indexing-policy-examples)一文。|
|存储的总数据 (每个区域)|单个区域中存储的总估计数据 (GB)。|
|工作负荷模式|如果工作负荷量是固定的, 请选择 "**稳定**"。 <br/><br/> 如果工作负荷量随着时间的推移而更改, 请选择 "**变量**"。  例如, 在特定的一天或一个月内。 <br/><br/> 如果选择 "可变工作负荷" 选项, 则可以使用下列设置:<ul><li>高峰时间百分比:工作负荷需要高峰 (最高) 吞吐量的月中的时间百分比。 <br/><br/> 例如, 如果工作负荷的工作负荷在上午9点到下午6点, 则高峰时间的百分比是:45小时/730 小时/月 = ~ 6%。<br/><br/></li><li>每个区域的每秒读取次数 (每秒预计的读取次数为高峰)。</li><li>每个区域的每秒写入次数–每秒预计的最大写入数。</li><li>每个区域的每秒读取数-每个区域不到峰值–每秒在非高峰期的读取次数。</li><li>每个区域一次写入/秒关闭高峰–每秒预期的写入数在非高峰期。</li></ul>使用高峰和非高峰时间间隔, 你可以通过[以编程方式调整预配的吞吐量](set-throughput.md#update-throughput-on-a-database-or-a-container)来优化成本。|
|项大小|数据项 (如文档) 的大小, 范围为 1 KB 到 2 MB。 <br/><br/>你还可以**上传示例 (JSON)** 文档以获得更准确的估计。<br/><br/>如果你的工作负荷在同一容器中具有多个类型的项 (包含不同的 JSON 内容), 则可以上传多个 JSON 文档并获得估计。 使用 "**添加新项**" 按钮添加多个示例 JSON 文档。|

你还可以使用 "**保存评估**" 按钮下载包含当前评估的 CSV 文件。 

![容量规划器高级模式](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Azure Cosmos DB 容量规划器中显示的价格是基于吞吐量和存储的公共定价费率进行评估的。 所有价格显示为美元。 请参阅[Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)查看按区域显示的所有费用。  

## <a name="estimating-throughput-for-queries"></a>估计查询吞吐量

Azure Cosmos 容量计算器假定点读取 (一项的读取, 如文档、按 ID 和分区键值) 和写入工作负荷。 若要估计查询所需的吞吐量, 请对 Cosmos 容器中的代表性数据集运行查询, 并[获取 RU 费用](find-request-unit-charge.md)。 将 RU 费用乘以你预计每秒运行的查询数, 以获取所需的总 RU 数。 

例如, 如果工作负荷需要查询, ``SELECT * FROM c WHERE c.id = 'Alice'``该查询每秒运行100次, 并且查询的 RU 费用为 10 ru, 则需要 100 query/sec * 10 RU/query = 1000 RU/秒来满足这些请求。 向在工作负荷中发生的任何读取或写入操作所需的 RU/s 添加 RU/秒。

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure Cosmos DB 的定价模型](how-pricing-works.md)。
* 创建新的[Cosmos 帐户、数据库和容器](create-cosmosdb-resources-portal.md)。
* 了解如何[优化预配的吞吐量成本](optimize-cost-throughput.md)。
* 了解如何[通过保留容量优化成本](cosmos-db-reserved-capacity.md)。

