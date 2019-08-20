---
title: 选择 Azure 搜索服务的定价层或 SKU - Azure 搜索
description: 可在以下 SKU 中预配 Azure 搜索：“免费”、“基本”和“标准”，其中“标准”在各种资源配置和容量级别中均可用。
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: a874c8a1fe2e8a81e2f42b2c88447fd52b47f3ad
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611964"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>选择 Azure 搜索的定价层

创建 Azure 搜索服务时, 会在服务生存期固定的定价层 (或 SKU) 上[创建资源](search-create-service-portal.md)。 层包括“免费”、“基本”、“标准”和“存储优化”。 “标准”和“存储优化”提供多种配置和容量。

大多数客户从“免费”层着手，以便能够评估该服务。 评估后, 在开发和生产部署的更高级别中创建第二个服务是很常见的。

尽管所有层（包括“免费”层）通常会提供功能奇偶一致性，但较大的工作负荷可以要求使用较高的层。 例如,[使用认知服务的 AI 扩充](cognitive-search-concept-intro.md)具有长时间运行的技能, 在免费服务的情况下超时, 除非数据集很小。

> [!NOTE] 
> 功能奇偶一致性的例外情况是[索引器](search-indexer-overview.md)，它们不可用于 S3 HD。
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>可用层

层反映托管服务（而不是功能）的硬件的特征，并按以下标准进行区分：

+ 可以创建的索引和索引器数量
+ 分区（物理存储）的大小和速度

您选择的层决定了计费费率。 Azure 门户中的以下屏幕截图显示可用层, 而不是定价 (可在门户中找到, 也可以在[定价页](https://azure.microsoft.com/pricing/details/search/)上找到)。 "**免费**"、"**基本**" 和 "**标准**" 是最常用的层。

**免费**在群集上创建受限的搜索服务, 与其他订阅者共享。 你可以完成小型项目, 包括快速入门和教程, 但无法缩放服务或运行大量工作负荷。 "**基本**" 和 "**标准**" 是最常用的可计费层, 默认值为 "**标准**"。

![Azure 搜索定价层](media/search-sku-tier/tiers.png "Azure 搜索定价层")

某些层针对某些类型的工作进行了优化。 例如,**标准3高密度 (S3 HD)** 是 S3 的*托管模式*, 其中基础硬件针对大量的小型索引进行了优化, 适用于多租户方案。 S3 HD 的每单位费用与 S3 相同，但硬件经过优化，可基于大量的小型索引快速读取文件。

**存储优化**层提供更大的存储容量, 其价格低于每 TB 标准级别。 主要弊端是查询延迟更高，应根据具体的应用程序要求确认这种延迟。  若要详细了解此层的性能注意事项，请参阅[性能和优化注意事项](search-performance-optimization.md)。

预配服务时，可以在[定价页](https://azure.microsoft.com/pricing/details/search/)、[Azure 搜索中的服务限制](search-limits-quotas-capacity.md)以及门户页上找到有关各个层的详细信息。

## <a name="billable-events"></a>计费事件

基于 Azure 搜索构建的解决方案可能会按以下方式产生成本:

+ 最小配置的服务基本成本
+ 增加时增加成本 (添加副本或分区)
+ 出站数据传输的带宽费用
+ 利用认知服务资源的认知搜索

### <a name="service-costs"></a>服务成本

与可以 "暂停" 以避免收费的虚拟机或其他资源不同, Azure 搜索服务在专用于专用的硬件上始终可用。 因此, 创建服务是一种可计费事件, 该事件在你创建服务时开始, 在你删除服务时结束。 

最小费用是按计费费率的第一个搜索单位 (1 个副本 x 个分区)。 在服务的整个生命周期内，此最低费用都是固定的，因为服务不能在低于此配置的组件上运行。 除了最小值以外, 还可以单独添加副本和分区。 通过副本和分区增加容量增加时, 会根据以下公式增加帐单: [(副本 x 分区 x 速率)](#search-units), 根据所选的定价层, 你需要支付的费用取决于所选的定价层。

在估算搜索解决方案的费用时，请记住，定价和容量不是线性的。 （容量翻倍不是支付两倍的费用，而是要支付更高的费用）有关该公式的工作方式示例，请参阅[如何分配副本和分区](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)。

### <a name="bandwidth-charges"></a>带宽费用

使用 [Azure 搜索索引器](search-indexer-overview.md)可能会影响计费，具体取决于服务的位置。 如果在数据所在的同一区域中创建 Azure 搜索服务，则可以完全消除数据流出费用。 下面是[带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中的一些信息:

+ Microsoft 不会向 Azure 上的任何服务或来自 Azure 搜索的任何出站数据收费。
+ 在 multiservice 解决方案中, 如果所有服务都位于同一区域, 则不会对跨网络数据进行收费。

如果服务在不同的区域中，则会针对出站数据收费。 这些费用实际上不是 Azure 搜索帐单的一部分。 此处之所以提到这些费用，是因为如果你使用数据或 AI 扩充索引器从不同的区域提取数据，将会在总体帐单中看到这些费用。

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>认知搜索 AI 扩充与认知服务

对于[与认知服务的 AI 扩充](cognitive-search-concept-intro.md), 应计划[将可计费的 azure 认知服务资源附加](cognitive-search-attach-cognitive-services.md)到在 Azure 搜索所在的同一区域中, 以实现即用即付处理。 附加认知服务不会产生固定的费用。 只需支付所需的处理费。

| 操作 | 计费影响 |
|-----------|----------------|
| 文档破解, 文本提取 | 免费 |
| 文档破解, 图像提取 | 根据从文档中提取的图像数量进行计费。 在[索引器配置](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)中，**imageAction** 是触发图像提取的参数。 如果 **imageAction** 设置为“none”（默认值），则不收取图像提取费用。 Azure 搜索的 "[定价详细信息](https://azure.microsoft.com/pricing/details/search/)" 页上介绍了图像提取速率。|
| [预先构建认知技能](cognitive-search-predefined-skills.md) | 按照与你使用认知服务直接执行任务相同的费率进行计费。 |
| 自定义技能 | 自定义技能是您提供的功能。 使用自定义技术的成本完全取决于自定义代码是否调用其他计量服务。 |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>计费公式 (R x P = SU)

对于 Azure 搜索操作，要了解的最重要计费概念是搜索单位 (SU)。 由于 Azure 搜索必须同时使用副本和分区进行索引编制和查询，因此无法按其中的一个进行计费。 相反，应基于两者的组合来计费。

SU 是服务使用的副本数和分区数的乘积： **(R x P = SU)** 。

每个服务至少从 1 个 SU（1 个分区乘以 1 个副本）开始。 任何服务的最大 SU 值为 36。 可通过多种方式来实现此最大值：例如，6 个分区 x 6 个副本，或 3 个分区 x 12 个副本。 通常使用小于总容量的值（例如，3 副本、3 分区的服务按 9 个 SU 计费）。 有关有效的组合，请参阅[分区和副本组合](search-capacity-planning.md#chart)图表。

费率按每个 SU、按小时计算。 每个层的费率渐进式提高。 层越高，分区越大且速度越快，因此，每小时的总费率更高。 可以在[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上查看每个层的费率。

大多数客户只是联机使用一部分总容量，将剩余的容器保持预留状态。 在计费方面，支付的每小时费用取决于联机的分区和副本数量（使用 SU 公式计算）。

## <a name="how-to-manage-and-reduce-costs"></a>如何管理和降低成本

除了以下建议, 请访问[计费和成本管理](https://docs.microsoft.com/azure/billing/billing-getting-started)。

- 在同一区域中创建所有资源, 或在尽可能少的区域中创建所有资源, 以最大程度地减少或消除带宽费用。

- 将所有服务合并为一个资源组, 例如 Azure 搜索、认知服务和解决方案中使用的任何其他 Azure 服务。 在 Azure 门户中, 找到资源组, 并使用**成本管理**命令了解实际和预计支出。

- 考虑将 Azure Web 应用用于前端应用程序, 以便请求和响应保留在数据中心边界内。

- 增加资源密集型操作 (如索引), 然后对常规查询工作负荷进行调整。 首先使用 Azure 搜索的最低配置 (一个 SU 由一个分区和一个副本), 然后监视用户活动以确定使用模式, 这些模式将指示需要更多的容量。 如果有可预测的模式, 则可以将缩放与活动同步 (需要编写代码来自动执行此操作)。

不能关闭搜索服务来减少帐单。 专用资源始终运行，是在服务的生存期内专门分配给你使用的。 就服务本身而言, 降低帐单的唯一方法是将副本和分区减少到仍能提供可接受的性能和[SLA 符合性](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的级别, 或在较低层创建服务 (S1 小时费率低于 S2 或 S3 速率)。 假设你预配了一个面向低端负载预测的服务。若要扩充该服务，可以创建另一个具有较大层的服务，在该服务上重建索引，然后删除第一个服务。

## <a name="how-to-evaluate-capacity-requirements"></a>如何评估容量需求

在 Azure 搜索中，容量的结构包括副本和分区。

+ 副本是搜索服务的实例。 每个副本托管索引的一个负载均衡副本。 例如，包含 6 个副本的服务具有加载到服务中的每个索引的 6 个副本。

+ 分区存储索引，并自动拆分可搜索的数据。 两个分区可将索引拆分成两半，三个分区可将索引拆分为三个部分，依次类推。 在容量方面，分区大小是各层级的主要区别特征。

> [!NOTE]
> 所有“标准”和“存储优化”层都支持[副本和分区的灵活组合](search-capacity-planning.md#chart)，因此你可以通过改变平衡方式来[优化系统以提高速度或存储](search-performance-optimization.md)。 “基本”层最多提供三个副本来实现高可用性，但只有一个分区。 “免费”层不提供专用资源：计算资源由多个订阅者共享。

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>评估容量

容量和运行服务的成本。 层在两个级别施加限制：存储和资源。 应该同时考虑到此两者，因为首先达到的限制就是实施的限制。

业务需求通常决定了所需的索引数。 例如，你可能需要对一个较大的文档存储库使用全局索引。 或者，你可能需要多个基于区域、应用或商业利基的索引。

若要确定索引大小，必须[生成一个索引](search-create-index-portal.md)。 Azure 搜索中的数据结构主要是[倒排索引](https://en.wikipedia.org/wiki/Inverted_index)结构，它具有与源数据不同的特征。 对于倒排索引，大小和复杂度由内容决定，不一定是输入的数据量。 具有高度冗余的大型数据源可能会导致比包含高度可变内容的较小数据集更小的索引。 因此，很难根据原始数据集的大小来推断索引大小。

> [!NOTE] 
> 即使估算将来的索引和存储需求类似于猜测，但也值得一试。 如果层级容量经证实过低，将需要在更高的层级上预配新服务，然后[重新加载索引](search-howto-reindex.md)。 服务无法从一个 SKU 就地升级到另一个。
>

### <a name="estimate-with-the-free-tier"></a>免费层评估

估算容量的一种方法是从“免费”层开始。 回想一下，“免费”服务最多提供 3 个索引、50 MB 存储和 2 分钟索引时间。 使用这些约束来估算预计的索引大小可能会很困难, 但这些步骤如下:

+ [创建免费服务](search-create-service-portal.md)。
+ 准备小型的、代表性的数据集。
+ [在门户中生成初始索引](search-create-index-portal.md), 并记下其大小。 功能和特性会影响存储。 例如, 添加建议器 (typeahead) 将增加存储需求。 使用相同的数据集, 您可以尝试创建索引的多个版本, 每个字段都有不同的属性, 以了解存储要求的变化情况。 有关详细信息, 请参阅[创建基本索引中的 "存储影响"](search-what-is-an-index.md#storage-implications)。

使用粗略估计时, 可以将两个索引 (开发和生产) 的预算翻倍, 然后相应地选择层。

### <a name="estimate-with-a-billable-tier"></a>使用可计费层估算

专用资源可以容纳更大的采样和处理时间, 以在开发过程中更逼真地估计索引数量、大小和查询量。 有些客户会直接进入可计费的层, 然后在开发项目成熟时重新评估。

1. [检查每个层级的服务限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits)以确定较低层级是否可以支持需要的索引数量。 在“基本”、“S1”和“S2”层中，索引数限制分别为 15、50 和 200。 “存储优化”层的索引数限制为 10 个，因为它旨在支持少量的极大型索引。

1. [在可计费层中创建服务](search-create-service-portal.md)：

    + 如果你不确定预计的负载, 请从 Basic 或 S1 开始。
    + 如果你知道会出现较大的索引和查询负载，请从较高的“S2”甚至“S3”层着手。
    + 如果你要为大量的数据编制索引并且查询负载相对较低（例如，使用与内部商务应用程序时），请从“优化存储”层 L1 或 L2 着手。

1. [生成初始索引](search-create-index-portal.md)以确定将源数据转换为索引的方式。 这是估计索引大小的唯一方法。

1. 在门户中[监视存储、服务限制、查询量和延迟](search-monitor-usage.md)。 门户会显示每秒查询数、限制的查询数和搜索延迟。 所有这些值可帮助你确定是否选择了合适的层。 此外可以通过启用[搜索流量分析](search-traffic-analytics.md)，要配置对点击率分析等值的深度监视。

索引数量和大小对于分析而言同等重要。 这是因为，需要通过充分利用存储（分区）或通过最大化对资源（索引、索引器等）的限制来达到最大限制（以先发生者为准）。 门户可帮助你跟踪两者，并在“概述”页面上并排显示当前使用情况和最大限制。

> [!NOTE]
> 如果文档包含无关数据，存储要求可能会过高。 理想情况下，文档仅包含搜索体验所需的数据。 二进制数据不可搜索，因此应单独存储（也许可以存储在 Azure 表或 Blob 存储中）。 然后在索引中添加一个字段，用于保存对外部数据的 URL 引用。 单个文档的最大大小是 16 MB（如果在一次请求中批量上传了多个文档，则小于 16 MB）。 有关详细信息，请参阅 [Azure 搜索中的服务限制](search-limits-quotas-capacity.md)。
>

**查询量注意事项**

每秒查询数 (QPS) 在性能优化期间是一个重要的指标，但如果你预期查询量一开始就很高，则通常只需考虑到层。

“标准”层可以提供平衡的副本数和分区数。 可以添加副本来实现负载均衡，或添加分区进行并行处理，以此增大查询周转时间。 然后，可以在预配服务后优化性能。

如果你预期持续查询量一开始就很高，应考虑更高的由更强大硬件支持的“标准”层。 如果不会这些这种查询量，可以使分区和副本脱机，甚至切换到更低层的服务。 有关如何计算查询吞吐量的详细信息，请参阅 [Azure 搜索性能和优化](search-performance-optimization.md)。

“存储优化”层可用于大数据工作负荷，当查询延迟要求不太重要时，可以支持更大的总体可用索引存储。 仍应使用更多的副本进行负载均衡，并使用更多的分区进行并行处理。 然后，可以在预配服务后优化性能。

**服务级别协议**

免费层和预览功能不提供[服务级别协议 (sla)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。 对于所有可计费的层，SLA 将在用户为服务提供足够冗余时生效。 查询 (读取) Sla 需要两个或多个副本。 需要有三个或更多的副本用于查询和索引 (读写) Sla。 分区数不会影响 Sla。

## <a name="tips-for-tier-evaluation"></a>层级评估提示

+ 了解如何生成高效的索引，并了解哪些刷新方法的影响最小。 使用[搜索流量分析](search-traffic-analytics.md)获取有关查询活动的见解。

+ 允许围绕查询生成指标，并围绕使用模式收集数据（在营业期间执行查询，在非高峰期执行索引编制）。 使用此数据做出明智的服务预配决策。 尽管这种做法不是在每小时或每日都可行，但可以动态调整分区和资源，以应对查询量的计划内变化。 此外，还可以应对计划外的但持续性的变化，前提是变化程度持续足够长的时间，以致有必要采取措施。

+ 请记住，预配不足的唯一缺点是，如果实际要求超出预测，则可能必须关闭某项服务。 为避免服务中断，可以在更高层级的相同订阅中创建新服务，并将其并行运行，直到所有应用和请求都指向新的终结点。

## <a name="next-steps"></a>后续步骤

从“免费”层开始，使用数据子集生成初始索引以了解其特征。 Azure 搜索中的数据结构是一种倒排索引结构。 倒排索引的大小和复杂性由内容决定。 请记住，高度冗余的内容往往会导致比高度不规则的内容更小的索引。 因此，确定索引存储要求的是它的内容特征而不是数据集的大小。

初始估算索引大小后，根据本文中所述的某个层[预配可计费的服务](search-create-service-portal.md)：“基本”、“标准”或“存储优化”。 放宽对数据大小的任何人为约束，并[重建索引](search-howto-reindex.md)以包含可搜索的所有数据。

根据需要[分配分区和副本](search-capacity-planning.md)以获取所需性能和规模。

如果性能和容量都合适，那么你已完成操作。 否则，请在与你的需求更接近的不同层级上重新创建搜索服务。

> [!NOTE]
> 如果有疑问, 请发布到[StackOverflow](https://stackoverflow.com/questions/tagged/azure-search)或[联系 Azure 支持部门](https://azure.microsoft.com/support/options/)。
