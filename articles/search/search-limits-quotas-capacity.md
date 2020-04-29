---
title: 层和 SKU 的服务限制
titleSuffix: Azure Cognitive Search
description: 用于容量计划的服务限制以及请求和响应 Azure 认知搜索的最大限制。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549057"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Azure 认知搜索中的服务限制

对存储、工作负载以及索引和其他对象数量的最大限制，取决于是在“免费”、“基本”、“标准”还是“存储优化”定价层上[预配 Azure 认知搜索](search-create-service-portal.md)     。

+ **免费**层是 Azure 订阅随附的多租户共享服务。 索引和查询请求针对其他租户使用的副本和分区执行。

+ “基本”层为较小规模的生产工作负荷提供专用计算资源，但与其他租户共享某些网络基础结构。 

+ **标准**层在专用计算机上运行，在每个级别上都具有更多存储和处理容量。 标准层共有四个级别：S1、S2、S3 和 S3 HD。

+ **存储优化**层在专用计算机上运行，与**标准**层相比具有更多的总存储、存储带宽和内存。 “存储优化”层分为两个级别：L1 和 L2

> [!NOTE]
> 从 7 月 1 日起，所有层通常都可用，包括存储优化层。 所有定价都可以在[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上找到。

  S3 高密度（S3 HD）针对特定工作负荷进行设计：[多租户](search-modeling-multitenant-saas-applications.md)和大量的小型索引（每个服务的3000个索引）。 此层未提供[索引器功能](search-indexer-overview.md)。 在 S3 HD 上，数据引入必须利用推送方式，使用 API 调用将数据从源推送到索引。 

> [!NOTE]
> 服务已在特定层上进行预配。 跳转层以获取容量涉及预配新的服务（不会就地升级）。 有关详细信息，请参阅[选择 SKU 或层](search-sku-tier.md)。 若要详细了解在已预配的服务内调整容量，请参阅[缩放查询和为工作负荷编制索引的资源级别](search-capacity-planning.md)。
>

## <a name="subscription-limits"></a>订阅限制
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>存储限制
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>索引限制

| 资源 | 免费 | 基本&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| 最大索引数 |3 |5 或 15 |50 |200 |200 |每个分区 1000，或者每个服务 3000 |10 个 |10 个 |
| 每个索引的简单字段的最大数目 |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| 每个索引的复杂集合字段的最大数目 |40 |40 |40 |40 |40 |40 |40 |40 |
| 每个文档的所有复杂集合的最大元素数目&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| 复杂字段的最大深度 |10 个 |10 个 |10 个 |10 个 |10 个 |10 个 |10 个 |10 个 |
| 每个索引最大[建议器](https://docs.microsoft.com/rest/api/searchservice/suggesters)数 |1 |1 |1 |1 |1 |1 |1 |1 |
| 每个索引的最大[计分配置文件](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)数 |100 |100 |100 |100 |100 |100 |100 |100 |
| 每个配置文件的最大函数数量 |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> 在 2017 年 12 月之前创建的基本服务的索引数限制较低（为 5 而不是 15）。 基本层是唯一具有下限（每个索引 100 个字段）的 SKU。

<sup>2</sup> 目前，在每个文档的复杂集合中使用极大量的元素会导致较高的存储用量。 这是已知问题。 同时，限制 3000 是所有服务层级的安全上限。 此限制仅对利用支持复杂类型字段 (`2019-05-06`) 的最早正式版 (GA) API 的索引操作强制实施。 为了避免中断可能正在使用早期 API 预览版（支持复杂类型字段）的客户端，我们不会对使用这些 API 预览版的索引操作强制实施此限制。 请注意，API 预览版并不旨在用于生产方案，我们强烈建议客户迁移到最新的 API 正式版。

> [!NOTE]
> 虽然单个索引的最大容量通常受可用存储空间的限制，但在单个索引中可以存储的文档总数有最大上限。 对于基本、S1、S2 和 S3 搜索服务2000000000，每个索引大约24000000000个文档，对于 S3HD 搜索服务，此限制为每个索引个文档。 复杂集合的每个元素都作为这些限制的目的，作为单独的文档计数。

<a name="document-limits"></a>

## <a name="document-limits"></a>文档限制 

从2018年10月开始，任何区域的任何可计费层（基本、S1、S2、S3、S3 HD）上创建的任何新服务都不再有任何文档计数限制。 早于10月2018之前创建的旧服务可能仍受文档计数限制的限制。

若要确定服务是否有文档数限制，请使用 [GET 服务统计信息 REST API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)。 文档数限制会在响应中反映出来，`null` 指示没有限制。

### <a name="document-size-limits-per-api-call"></a>每个 API 调用的文档大小限制

调用索引 API 时的最大文档大小大约为 16 MB。

文档大小实际上是索引 API 请求主体大小的限制。 由于可以将包含多个文档的批次传递给索引 API，因此大小限制实际上取决于批次中的文档数。 对于具有单个文档的批次，最大文档大小是 16 MB JSON。

要保持较低的文档大小，务必将不可查询数据从请求中排除。 图像和其他二进制数据不可直接查询，并且不应存储在索引中。 要将不可查询的数据集成到搜索结果中，请定义用于存储资源的 URL 引用的不可搜索字段。

## <a name="indexer-limits"></a>索引器限制

存在最长运行时间，目的是在总体上为服务提供平衡和稳定性，但较大的数据集需要的索引时间可能比最大值允许的要长。 如果在允许的最长时间内无法完成索引作业，请尝试按计划运行。 计划程序将跟踪索引的状态。 如果计划的索引作业因某种原因而中断，则索引器可以在下一次计划运行时从它上次停止的位置重新开始。


| 资源 | 免费&nbsp;<sup>1</sup> | 基本&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| 最大索引器数 |3 |5 或 15|50 |200 |200 |空值 |10 |10 |
| 最大数据源数 |3 |5 或 15 |50 |200 |200 |空值 |10 |10 |
| 最大技能集数<sup>4</sup> |3 |5 或 15 |50 |200 |200 |空值 |10 |10 |
| 每次调用的最大索引编制负载 |10,000 个文档 |仅受最大文档的限制 |仅受最大文档的限制 |仅受最大文档的限制 |仅受最大文档的限制 |空值 |无限制 |无限制 |
| 最小计划 | 5 分钟 |5 分钟 |5 分钟 |5 分钟 |5 分钟 |5 分钟 |5 分钟 | 5 分钟 |
| 最长运行时间 <sup>5</sup> | 1-3 分钟 |24 小时 |24 小时 |24 小时 |24 小时 |空值  |24 小时 |24 小时 |
| 认知搜索技能集的最长运行时间或具有图像分析的 blob 索引 <sup>5</sup> | 3-10 分钟 |2 小时 |2 小时 |2 小时 |2 小时 |空值  |2 小时 |2 小时 |
| Blob 索引器：最大 blob 大小，MB |16 |16 |128 |256 |256 |空值  |256 |256 |
| Blob 索引器：从 blob 中提取的内容的最大字符数 |32,000 |64,000 |4&nbsp;百万 |8&nbsp;百万 |1600&nbsp;万 |空值 |4&nbsp;百万 |4&nbsp;百万 |

<sup>1</sup> 对于免费服务，对于 blob 源，索引器最长执行时间为 3 分钟；对于所有其他数据源，索引器最长执行时间为为 1 分钟。 对于调用认知服务的 AI 索引，免费服务的限制是每天 20 个免费事务，而根据定义，一个事务是指一个成功通过扩充管道的文档。

<sup>2</sup> 在 2017 年 12 月之前创建的基本服务的索引器、数据源和技能集的数目限制较低（为 5 而不是 15）。

<sup>3</sup> S3 HD 服务未包括索引器支持。

<sup>4</sup> 每个技能集最多拥有 30 项技能。

<sup>5</sup> 认知搜索工作负载和 Azure blob 索引中的 图像分析的运行时间比常规文本索引运行时间短。 图像分析和自然语言处理属于计算密集型，并且消耗了过多的可用处理能力。 减少运行时间，以便队列中的其他作业能够运行。  

> [!NOTE]
> 如[索引限制](#index-limits)中所述，从支持复杂类型 (`2019-05-06`) 的最新 API 正式版开始，索引器还针对每个文档的所有复杂集合强制实施 3000 个元素的上限。 这意味着，如果你使用 API 旧版本创建了索引器，则不需要遵守此限制。 为了保持最高兼容性，使用 API 旧版本创建，然后使用 API 版本 `2019-05-06` 或更高版本更新的索引器，仍会从这些限制中**排除**。 客户应注意使用极大复杂集合所造成的负面影响（如前所述）；我们强烈建议使用最新 API 正式版创建任何新索引器。

## <a name="synonym-limits"></a>同义词限制

允许的同义词映射的最大数量因定价层而异。 每个规则最多可以有 20 个扩展，一个扩展就是一个意义相同的词。 例如，给定“猫”，与“猫咪”、“猫科动物”和“猫属”（猫的属）的关联将算作 3 个扩展。

| 资源 | 免费 | 基本 | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| 最大同义词映射数 |3 |3|5 |10 |20 |20 | 10 | 10 |
| 每个映射的最大规则数 |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>每秒查询次数 (QPS)

每个客户必须独立制定 QPS 估计值。 索引大小和复杂性、查询大小和复杂性以及流量大小是 QPS 的主要决定因素。 当此类因素未知时，没有方法能提供有意义的估计值。

针对专用资源（基本层和标准层）上运行的服务进行计算时，估计值更可预测。 由于能够控制更多参数，因此可以更精确地评估 QPS。 有关如何进行估算的指导，请参阅 [Azure 认知搜索的性能和优化](search-performance-optimization.md)。

与“标准”层相比，“存储优化”层预计会有较低的查询吞吐量，较高的延迟。  估计会体验到的查询性能的方法与“标准”层相同。

## <a name="data-limits-ai-enrichment"></a>数据限制（AI 扩充）

用于调用[实体识别](cognitive-search-skill-entity-recognition.md)、[关键短语提取](cognitive-search-skill-keyphrases.md)、[情绪分析](cognitive-search-skill-sentiment.md)、[语言检测](cognitive-search-skill-language-detection.md)和[PII 检测](cognitive-search-skill-pii-detection.md)的文本分析资源的[AI 扩充管道](cognitive-search-concept-intro.md)受数据限制的限制。 记录的最大大小应为50000个字符[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果需要在将数据发送到情绪分析器之前拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="throttling-limits"></a>限制

当系统接近峰值容量时，搜索查询和索引请求会受到限制。 对不同 API 的限制行为各不相同。 根据服务中的负载动态限制查询 API（搜索/建议/自动完成）和索引 API。 索引 API 的请求速率限制是静态的。 

索引相关操作的静态速率请求限制：

+ 列出索引（GET/indexes）：每个搜索单位每秒5个
+ 获取索引（GET/indexes/myindex）：每个搜索单位每秒10次
+ Create Index （POST/indexes）：每个搜索单位每分钟12个
+ 创建或更新索引（PUT/indexes/myindex）：每个搜索单位每秒6次
+ 删除索引（删除/indexes/myindex）：每个搜索单位每分钟12个 

## <a name="api-request-limits"></a>API 请求限制
* 每个请求最大 16 MB <sup>1</sup>
* 最大 8 KB URL 长度
* 每个索引上传、合并或删除的批次最多包含 1000 个文档
* $Orderby 子句中最多 32 字段
* 最大搜索词大小为 UTF-8 编码文本的 32,766 字节（32 KB 减 2 个字节）

<sup>1</sup> 在 Azure 认知搜索中，请求主体受 16 MB 上限的约束，这会针对不受理论限制约束的单个字段或集合的内容施加实际限制（有关字段组合和限制的详细信息，请参阅[支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)）。

## <a name="api-response-limits"></a>API 响应限制
* 每页搜索结果最多返回 1000 个文档
* 每个建议 API 请求最多返回 100 条建议

## <a name="api-key-limits"></a>API 密钥限制
API 密钥用于服务身份验证。 有两种类型。 管理密钥在请求标头中指定，并授予对服务的完全读写访问权限。 查询密钥是只读密钥并在 URL 上指定，并且通常分发给客户端应用程序。

* 每个服务最多 2 个管理密钥
* 每个服务最多 50 个查询密钥