---
title: Azure 搜索中的服务限制 | Microsoft Docs
description: 用于容量计划的服务限制以及请求和响应 Azure 搜索的最大限制。
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: heidist
ms.openlocfilehash: 23f67099bff7cdadcee44eed50e9944ecf7c5672
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="service-limits-in-azure-search"></a>Azure 搜索中的服务限制
对存储、工作负荷以及索引、文档和其他对象数量的最大限制，取决于是在“免费”、“基本”还是“标准”定价层上[预配 Azure 搜索](search-create-service-portal.md)。

+ **免费**层是 Azure 订阅随附的多租户共享服务。

+ **基本**层为小规模生产工作负荷提供专用计算资源。

+ **标准**层在专用计算机上运行，在每个级别上都具有更多存储和处理容量。 标准层共有四个级别：S1、S2、S3 和 S3 HD。

  S3 高密度 (S3 HD) 是针对特定工作负荷设计的：[多租户](search-modeling-multitenant-saas-applications.md)和大量的小索引（每个索引一百万个文档，每个服务三千个索引）。 此层未提供[索引器功能](search-indexer-overview.md)。 在 S3 HD 上，数据引入必须利用推送方式，使用 API 调用将数据从源推送到索引。 

> [!NOTE]
> 服务已在特定层上进行预配。 跳转层以获取容量涉及预配新的服务（不会就地升级）。 有关详细信息，请参阅[选择 SKU 或层](search-sku-tier.md)。 若要详细了解在已预配的服务内调整容量，请参阅[缩放查询和为工作负荷编制索引的资源级别](search-capacity-planning.md)。
>

## <a name="subscription-limits"></a>订阅限制
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>存储限制
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>索引限制

| 资源 | 免费 | 基本&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| 最大索引数 |3 |5 或 15 |50 |200 |200 |每个分区 1000，或者每个服务 3000 |
| 每个索引的最大字段 |1000 |100 |1000 |1000 |1000 |1000 |
| 每个索引的最大计分配置文件 |100 |100 |100 |100 |100 |100 |
| 每个配置文件的最大函数数量 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> 2017 年底后创建的基本服务的上限已增大为 15 个索引、数据源和索引器。 之前创建的服务的上限为 5。 基本层是唯一具有下限（每个索引 100 个字段）的 SKU。

## <a name="document-limits"></a>文档限制 

在大多数区域中，对于在 2017 年 11 月/12 月之后创建的所有服务，Azure 搜索定价层（基本、S1、S2、S3、S3 HD）对文档数没有限制。 本部分介绍了施加了限制的区域以及如何确定你的服务是否受影响。 

若要确定你的服务是否具有文档数限制，请检查你的服务的概述页中的“使用情况”磁贴。 文档数可能不受限制，也可能基于层受限于某个限制。

  ![“使用情况”磁贴](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>具有文档数限制的区域和服务

具有限制的服务是在 2017 年底之前创建的，或者是在使用较低容量群集来承载 Azure 搜索服务的数据中心内运行。 受影响的数据中心位于以下区域：

+ 澳大利亚东部
+ 东亚
+ 印度中部
+ 日本西部
+ 美国中西部

对于具有文档数限制的服务，将应用以下上限：

|  免费 | 基本 | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1 百万 |每个分区 1500 万，或者每个服务 1 亿 8 千万 |每个分区 6 千万，或者每个服务 7 亿 2 千万 |每个分区 1 亿 2 千万，或者每个服务 14 亿 |每个索引 1 百万，或者每个分区 2 亿 |

> [!Note] 
> 对于 2017 年底后创建的 S3 高密度服务，已去除每个分区 2 亿个文档的限制，但每个索引 100 万个文档的限制保留。


### <a name="document-size-limits-per-api-call"></a>每个 API 调用的文档大小限制

调用索引 API 时的最大文档大小大约为 16 MB。

文档大小实际上是索引 API 请求主体大小的限制。 由于可以将包含多个文档的批次传递给索引 API，因此大小限制实际上取决于批次中的文档数。 对于具有单个文档的批次，最大文档大小是 16 MB JSON。

要保持较低的文档大小，务必将不可查询数据从请求中排除。 图像和其他二进制数据不可直接查询，并且不应存储在索引中。 要将不可查询的数据集成到搜索结果中，请定义用于存储资源的 URL 引用的不可搜索字段。

## <a name="indexer-limits"></a>索引器限制

2017 年底后创建的基本服务增加了限制，即 15 个索引、数据源和索引器。

| 资源 | 免费&nbsp;<sup>1</sup> | 基本&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| 最大索引器数 |3 |5 或 15|50 |200 |200 |不适用 |
| 最大数据源数 |3 |5 或 15 |50 |200 |200 |不适用 |
| 每次调用的最大索引编制负载 |10,000 个文档 |仅受最大文档的限制 |仅受最大文档的限制 |仅受最大文档的限制 |仅受最大文档的限制 |不适用 |
| 最长运行时间 | 1-3 分钟 |24 小时 |24 小时 |24 小时 |24 小时 |不适用  |
| Blob 索引器：最大 blob 大小，MB |16 |16 |128 |256 |256 |不适用  |
| Blob 索引器：从 blob 中提取的内容的最大字符数 |32,000 |64,000 |4 百万 |4 百万 |4 百万 |不适用 |

<sup>1</sup> 对于免费服务，对于 blob 源，索引器最长执行时间为 3 分钟；对于所有其他数据源，索引器最长执行时间为为 1 分钟。

<sup>2</sup> 2017 年底后创建的基本服务的上限已增大为 15 个索引、数据源和索引器。 之前创建的服务的上限为 5。

<sup>3</sup> S3 HD 服务未包括索引器支持。

## <a name="queries-per-second-qps"></a>每秒查询次数 (QPS)

每个客户必须独立制定 QPS 估计值。 索引大小和复杂性、查询大小和复杂性以及流量大小是 QPS 的主要决定因素。 当此类因素未知时，没有方法能提供有意义的估计值。

针对专用资源（基本层和标准层）上运行的服务进行计算时，估计值更可预测。 由于能够控制更多参数，因此可以更精确地评估 QPS。 有关如何方法估算的指南，请参阅 [Azure 搜索的性能和优化](search-performance-optimization.md)。

## <a name="api-request-limits"></a>API 请求限制
* 每个请求最大 16 MB <sup>1</sup>
* 最大 8 KB URL 长度
* 每个索引上传、合并或删除的批次最多包含 1000 个文档
* $Orderby 子句中最多 32 字段
* 最大搜索词大小为 UTF-8 编码文本的 32,766 字节（32 KB 减 2 个字节）

<sup>1</sup> 在 Azure 搜索中，请求主体受 16 MB 上限的约束，这会针对不受理论限制约束的单个字段或集合的内容施加实际限制（有关字段组合和限制的详细信息，请参阅[支持的数据类型](https://msdn.microsoft.com/library/azure/dn798938.aspx)）。

## <a name="api-response-limits"></a>API 响应限制
* 每页搜索结果最多返回 1000 个文档
* 每个建议 API 请求最多返回 100 条建议

## <a name="api-key-limits"></a>API 密钥限制
API 密钥用于服务身份验证。 有两种类型。 管理密钥在请求标头中指定，并授予对服务的完全读写访问权限。 查询密钥是只读密钥并在 URL 上指定，并且通常分发给客户端应用程序。

* 每个服务最多 2 个管理密钥
* 每个服务最多 50 个查询密钥
