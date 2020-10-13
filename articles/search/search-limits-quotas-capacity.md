---
title: 层和 SKU 的服务限制
titleSuffix: Azure Cognitive Search
description: 用于容量计划的服务限制，以及 Azure 认知搜索请求和响应的最大限制。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 6c422b9a70f679279d1310444aafb1f9131ff944
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949844"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Azure 认知搜索中的服务限制

对存储、工作负荷以及索引和其他对象的数量的最大限制取决于你是在“免费”、“基本”、“标准”还是“存储优化”定价层上[预配 Azure 认知搜索](search-create-service-portal.md)   。

+ **免费**层是 Azure 订阅随附的多租户共享服务。 

+ “基本”层为较小规模的生产工作负荷提供专用计算资源，但与其他租户共享某些网络基础结构。

+ **标准**层在专用计算机上运行，在每个级别上都具有更多存储和处理容量。 标准层共有四个级别：S1、S2、S3 和 S3 HD。 S3 高密度 (S3 HD) 旨在用于[多租户](search-modeling-multitenant-saas-applications.md)方案，以及较大数量的小型索引（每个服务 3000 个索引）。 S3 HD 不提供[索引器功能](search-indexer-overview.md)，数据引入必须利用可将数据从源推送到索引的 API。 

+ “存储优化”层在总存储量、存储带宽和内存量比“标准”层更高的专用计算机上运行。  此层面向缓慢变化的大型索引。 “存储优化”层分为两个级别：L1 和 L2。

## <a name="subscription-limits"></a>订阅限制
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>存储限制
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>索引限制

| 资源 | 免费 | 基本&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| 最大索引数 |3 |5 或 15 |50 |200 |200 |每个分区 1000，或者每个服务 3000 |10 |10 |
| 每个索引的最大简单字段数目 |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| 每个索引的最大复杂集合字段数目 |40 |40 |40 |40 |40 |40 |40 |40 |
| 每个文档的所有复杂集合的最大元素数目&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| 复杂字段的最大深度 |10 |10 |10 |10 |10 |10 |10 |10 |
| 每个索引最大[建议器](/rest/api/searchservice/suggesters)数 |1 |1 |1 |1 |1 |1 |1 |1 |
| 每个索引的最大[计分配置文件](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)数 |100 |100 |100 |100 |100 |100 |100 |100 |
| 每个配置文件的最大函数数量 |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>2</sup> 在 2017 年 12 月之前创建的基本服务对索引数的限制较低（为 5 个而不是 15 个）。 基本层是唯一具有下限（每个索引 100 个字段）的 SKU。

<sup>2</sup> 元素存在上限，因为有大量它们会大幅增加索引所需的存储空间。 复杂集合的元素定义为该集合的成员。 例如，假设有一个 [旅馆文档使用房间复杂集合](search-howto-complex-data-types.md#indexing-complex-types)，则房间集合中的每个房间都被视为一个元素。 在编制索引期间，索引引擎可以安全地在整个文档中处理最多3000个元素。 [此限制](search-api-migration.md#upgrade-to-2019-05-06) 是在中引入的， `api-version=2019-05-06` 并且仅适用于复杂集合，不适用于字符串集合或复杂字段。

<a name="document-limits"></a>

## <a name="document-limits"></a>文档限制 

自 2018 年 10 月起，在任何区域的任何可计费层（基本、S1、S2、S3、S3 HD）创建的任何新服务都不再有任何文档计数限制。 2018 年 10 月之前创建的旧服务可能仍受文档计数限制的约束。

若要确定服务是否有文档数限制，请使用 [GET 服务统计信息 REST API](/rest/api/searchservice/get-service-statistics)。 文档数限制会在响应中反映出来，`null` 表示没有限制。

> [!NOTE]
> 尽管该服务不会施加文档数限制，但在“基本”、S1、S2 和 S3 搜索服务中，每个索引的分片限制大约为 240 亿个文档。 对于 S3 HD，每个索引的分片限制为 20 亿个文档。 对于分片限制而言，复杂集合的每个元素都计为一个单独的文档。

### <a name="document-size-limits-per-api-call"></a>每个 API 调用的文档大小限制

调用索引 API 时的最大文档大小大约为 16 MB。

文档大小实际上是索引 API 请求主体大小的限制。 由于可以将包含多个文档的批次传递给索引 API，因此大小限制实际上取决于批次中的文档数。 对于具有单个文档的批次，最大文档大小是 16 MB JSON。

在估算文档大小时，请记得仅考虑那些可由搜索服务使用的字段。 应在计算中忽略源文档中的任何二进制文件或图像数据。  

## <a name="indexer-limits"></a>索引器限制

“最长运行时间”存在的目的是在总体上为服务提供平衡和稳定性，但较大的数据集所需的索引编制时间可能会超过最大值允许的时间。 如果在允许的最长时间内无法完成索引作业，请尝试按计划运行。 计划程序将跟踪索引的状态。 如果计划的索引作业因某种原因而中断，则索引器可以在下一次计划运行时从它上次停止的位置重新开始。


| 资源 | 免费&nbsp;<sup>1</sup> | 基本&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| 最大索引器数 |3 |5 或 15|50 |200 |200 |空值 |10 |10 |
| 最大数据源数 |3 |5 或 15 |50 |200 |200 |空值 |10 |10 |
| 最大技能集数<sup>4</sup> |3 |5 或 15 |50 |200 |200 |空值 |10 |10 |
| 每次调用的最大索引编制负载 |10,000 个文档 |仅受最大文档的限制 |仅受最大文档的限制 |仅受最大文档的限制 |仅受最大文档的限制 |空值 |无限制 |无限制 |
| 最小计划 | 5 分钟 |5 分钟 |5 分钟 |5 分钟 |5 分钟 |5 分钟 |5 分钟 | 5 分钟 |
| 最长运行时间| 1-3 分钟 |24 小时 |24 小时 |24 小时 |24 小时 |空值  |24 小时 |24 小时 |
| 包含技能组的索引器的最长运行时间 <sup>5</sup> | 3-10 分钟 |2 小时 |2 小时 |2 小时 |2 小时 |空值  |2 小时 |2 小时 |
| Blob 索引器：最大 blob 大小，MB |16 |16 |128 |256 |256 |空值  |256 |256 |
| Blob 索引器：从 blob 中提取的内容的最大字符数 |32,000 |64,000 |400&nbsp;万 |800&nbsp;万 |1600&nbsp;万 |空值 |400&nbsp;万 |400&nbsp;万 |

<sup>1</sup> 对于免费服务，对于 blob 源，索引器最长执行时间为 3 分钟；对于所有其他数据源，索引器最长执行时间为为 1 分钟。 对于调用认知服务的 AI 索引，免费服务的限制是每天 20 个免费事务（一个事务定义为一个成功通过扩充管道的文档）。

<sup>2</sup> 在 2017 年 12 月之前创建的基本服务在索引器、数据源和技能集方面的限制较低（为 5 个而不是 15 个）。

<sup>3</sup> S3 HD 服务未包括索引器支持。

<sup>4</sup> 每个技能集最多拥有 30 项技能。

<sup>5</sup> AI 扩充和图像分析属于计算密集型功能，会消耗过多的可用处理能力。 这些工作负荷的运行时间已缩短，从而使队列中的其他作业有更多的机会运行。

> [!NOTE]
> 如[索引限制](#index-limits)中所述，从支持复杂类型 (`2019-05-06`) 的最新 API 正式版开始，索引器还针对每个文档的所有复杂集合强制实施 3000 个元素的上限。 这意味着，如果你使用早期 API 版本创建了索引器，则不会受此限制约束。 为了保持最高兼容性，使用早期 API 版本创建并使用 API 版本 `2019-05-06` 或更高版本更新了的索引器，仍会从这些限制中**排除**。 客户应注意使用极大复杂集合所造成的负面影响（如前所述）；我们强烈建议使用最新 API 正式版创建任何新索引器。

### <a name="shared-private-link-resource-limits"></a>共享专用链接资源限制

> [!NOTE]
> 索引器可以通过[共享的专用链接资源 API](/rest/api/searchmanagement/sharedprivatelinkresources)安全地访问资源，如[本操作方法指南](search-indexer-howto-access-private.md)中所述

| 资源 | 免费 | 基本 | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 私有终结点索引器支持 | 否 | 是 | 是 | 是 | 是 | 否 | 是 | 是 |
| 使用技能组合<sup>1</sup>的索引器的专用终结点支持 | 否 | 否 | 否 | 是 | 是 | 否 | 是 | 是 |
| 最大专用终结点 | 不适用 | 10或30 | 100 | 400 | 400 | 不适用 | 20 | 20 |
| 最大不同资源类型<sup>2</sup> | 不适用 | 4 | 7 | 15 | 15 | 不适用 | 4 | 4 |

<sup>1</sup> AI 扩充和图像分析计算密集型，并使用数量不相称的可用处理能力，因此，对于较低的搜索服务层，将其设置为在专用环境中运行可能会对搜索服务的性能和稳定性产生不利影响。

<sup>2</sup> 不同资源类型的数量计算为 `groupId` 在给定搜索服务的所有共享专用链接资源中使用的唯一值的数目，而不考虑资源的状态。

## <a name="synonym-limits"></a>同义词限制

同义词映射的最大数量因层而异。 每个规则最多可以有 20 个扩展，一个扩展就是一个意义相同的词。 例如，在指定“猫”的情况下，与“猫咪”、“猫科动物”和“猫属”（猫的属）的关联将算作 3 个扩展。

| 资源 | 免费 | 基本 | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| 最大同义词映射数 |3 |3|5 |10 |20 |20 | 10 | 10 |
| 每个映射的最大规则数 |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>每秒查询次数 (QPS)

每个客户必须独立制定 QPS 估计值。 索引大小和复杂性、查询大小和复杂性以及流量大小是 QPS 的主要决定因素。 当此类因素未知时，没有方法能提供有意义的估计值。

针对专用资源（基本层和标准层）上运行的服务进行计算时，估计值更可预测。 由于能够控制更多参数，因此可以更精确地评估 QPS。 有关如何进行估算的指导，请参阅 [Azure 认知搜索的性能和优化](search-performance-optimization.md)。

与“标准”层相比，“存储优化”层（L1 和 L2）的查询吞吐量应更低，延迟应更高。

## <a name="data-limits-ai-enrichment"></a>数据限制（AI 扩充）

调用文本分析资源进行[实体识别](cognitive-search-skill-entity-recognition.md)、[关键短语提取](cognitive-search-skill-keyphrases.md)、[情绪分析](cognitive-search-skill-sentiment.md)、[语言检测](cognitive-search-skill-language-detection.md)和[个人信息检测](cognitive-search-skill-pii-detection.md)的 [AI 扩充管道](cognitive-search-concept-intro.md)会受到数据限制的约束。 记录的最大大小应为 50,000 个字符（通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量）。 如果需要在将数据发送到情绪分析器之前拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="throttling-limits"></a>限制

当系统接近峰值容量时，搜索查询和索引请求会受到限制。 对不同 API 的限制行为各不相同。 系统会根据服务的负载动态限制查询 API（搜索/建议/自动完成）和索引 API。 索引 API 有静态的请求速率限制。 

索引相关操作的静态速率请求限制：

+ 列出索引 (GET /indexes)：每个搜索单位每秒限制为 5 个
+ 获取索引 (GET /indexes/myindex)：每个搜索单位每秒限制为 10 个
+ 创建索引 (POST /indexes)：每个搜索单位每分钟限制为 12 个
+ 创建或更新索引 (PUT /indexes/myindex)：每个搜索单位每秒限制为 6 个
+ 删除索引 (DELETE /indexes/myindex)：每个搜索单位每分钟限制为 12 个 

## <a name="api-request-limits"></a>API 请求限制
* 每个请求最大 16 MB <sup>1</sup>
* 最大 8 KB URL 长度
* 每个索引上传、合并或删除的批次最多包含 1000 个文档
* $Orderby 子句中最多 32 字段
* 最大搜索词大小为 UTF-8 编码文本的 32,766 字节（32 KB 减 2 个字节）

<sup>1</sup> 在 Azure 认知搜索中，请求正文受 16 MB 上限的约束，这会针对不受理论限制约束的单个字段或集合的内容施加实际限制（有关字段组合和限制的详细信息，请参阅[支持的数据类型](/rest/api/searchservice/supported-data-types)）。

## <a name="api-response-limits"></a>API 响应限制
* 每页搜索结果最多返回 1000 个文档
* 每个建议 API 请求最多返回 100 条建议

## <a name="api-key-limits"></a>API 密钥限制
API 密钥用于服务身份验证。 有两种类型。 管理密钥在请求标头中指定，并授予对服务的完全读写访问权限。 查询密钥是只读密钥并在 URL 上指定，并且通常分发给客户端应用程序。

* 每个服务最多 2 个管理密钥
* 每个服务最多 50 个查询密钥