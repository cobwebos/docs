---
title: "Azure 搜索的容量规划 | Microsoft Docs"
description: "在 Azure 搜索中调整分区和副本计算机资源，其中每个资源按照可计费搜索单位定价。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 1dc16afe-56f9-439d-8874-1733ae1a2b74
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/08/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: 08682b7986cc2210ed21f254e2a9a63b5355e583
ms.openlocfilehash: bfed40417d800e86de7ef437c42162b1e1a0d886
ms.contentlocale: zh-cn
ms.lasthandoff: 02/24/2017

---

# <a name="scale-resource-levels-for-query-and-indexing-workloads-in-azure-search"></a>在 Azure 搜索中缩放用于查询和索引工作负荷的资源级别
[选择定价层](search-sku-tier.md)并[预配搜索服务](search-create-service-portal.md)后，下一步是有选择性地增加服务使用的副本或分区数目。 每一层提供固定数量的计费单位。 本文介绍如何通过分配这些单位来实现最佳配置，根据查询执行、索引和存储的要求做出平衡。

在[基本层](http://aka.ms/azuresearchbasic)或某个[标准层](search-limits-quotas-capacity.md)中设置服务时，可以使用资源配置。 对于这些层中的可计费服务，购买的容量以*搜索单位* (SU) 为增量，其中每个分区和副本被视为一个 SU。 

使用的 SU 越少，帐单费用也就相应地越少。 只要设置服务，就会产生费用。 如果暂时不使用某个服务，避免计费的唯一方法就是删除该服务，需要该服务时再重新创建。

> [!Note]
> 删除某个服务会删除该服务上的所有内容。 Azure 搜索中没有用于备份和还原持久化搜索数据的设施。 若要在新服务上重新部署现有索引，应当运行最初用来创建和加载该索引的程序。 

## <a name="terminology-partitions-and-replicas"></a>术语：分区和副本
分区和副本是支持搜索服务的主要资源。

| 资源 | 定义 |
|----------|------------|
|*分区* | 为读/写操作（例如在重建或刷新索引时）提供索引存储和 I/O。|
|*副本* | 是搜索服务的实例，主要用于对查询操作进行负载均衡。 每个副本始终托管索引的一个副本。 如果有 12 个副本，服务中会加载每个索引的 12 个副本。|

> [!NOTE]
> 无法直接操作或管理哪些索引在副本上运行。 每个副本托管索引的一个副本，这是服务体系结构的一部分。
>

## <a name="how-to-allocate-partitions-and-replicas"></a>如何分配分区和副本
在 Azure 搜索中，最初为服务分配了由一个分区和一个副本组成的最低级别的资源。 如果层支持这样做，可以递增方式调整计算资源：需要更多的存储和 I/O 时增加分区，或者增加副本来应对较大的查询卷或提供较好的性能。 单个服务必须具有足够的资源才能处理所有工作负荷（索引和查询）。 无法在多个服务之间细分工作负荷。

若要增加或更改副本和分区的分配，建议使用 Azure 门户。 该门户针对允许的组合强制实施限制，使其低于上限：

1. 登录到 [Azure 门户](https://portal.azure.com/)，然后选择搜索服务。
2. 在“设置”中打开“缩放”边栏选项卡，然后使用滑块来增加或减少分区和副本数目。

如果需要使用基于脚本或基于代码的预配方法，可以改用[管理 REST API](https://msdn.microsoft.com/library/azure/dn832687.aspx)。

一般而言，搜索应用程序所需的副本数多过分区数，尤其是在服务操作偏向于查询工作负荷的情况下。 [高可用性](#HA)部分将解释原因。

> [!NOTE]
> 预配服务后，无法升级到更高的 SKU。 需要在新层中创建搜索服务，然后重新加载索引。 有关服务预配的帮助，请参阅 [Create an Azure Search service in the portal](search-create-service-portal.md)（在门户中创建 Azure 搜索服务）。
>
>

<a id="HA"></a>

## <a name="high-availability"></a>高可用性
由于扩展的过程比较简单而且相对较快，因此我们通常建议从一个分区以及一个或两个副本开始，然后随着不断构建查询卷而进行扩展。 对于“基本”和 S1 层中的许多服务而言，一个分区即可提供足够的存储和 I/O（每个分区 1500 万个文档）。

查询工作负荷主要是在副本上运行。 如果需要更高的吞吐量或高可用性，也许需要增加副本。

针对高可用性的一般建议是：

* 对于只读工作负荷（查询），需要有两个副本才能实现高可用性
* 对于读/写工作负荷（查询以及添加、更新或删除单个文档时的索引编制），需有三个或更多个副本才能实现高可用性

Azure 搜索的服务级别协议 (SLA) 针对查询操作，以及由文档添加、更新或删除操作构成的索引更新。

### <a name="index-availability-during-a-rebuild"></a>重建期间的索引可用性

Azure 搜索的高可用性与查询以及不涉及重建索引的索引更新相关。 如果删除字段、更改数据类型或重命名字段，则需要重建索引。 若要重建索引，必须删除该索引，重新创建该索引，然后重新加载数据。

> [!NOTE]
> 可将新字段添加到 Azure 搜索索引，而无需重建索引。 对于索引中已存在的所有文档，新字段的值将为 null。

若要让索引在重建期间保持可用，相同的服务中必须存在不同名的索引副本，或者不同的服务中存在同名的索引，然后在代码中提供重定向或故障转移逻辑。

## <a name="disaster-recovery"></a>灾难恢复
目前没有任何内置的机制可实现灾难恢复。 添加分区或副本并不是实现灾难恢复目标的正确策略。 最常见的方法是通过在另一区域中设置另一个搜索服务，在服务级别添加冗余。 与索引重建期间的可用性一样，重定向或故障转移逻辑必须来自代码。

## <a name="increase-query-performance-with-replicas"></a>使用副本提高查询性能
如果出现查询延迟，则表明需要增加副本。 一般而言，改善查询性能的第一步是增加此类资源。 添加副本时，将有更多的索引副本联机，可支持更大的查询工作负荷，通过多个副本对请求进行负载均衡。

我们无法提供可靠的每秒查询数 (QPS) 预测：查询性能取决于查询和竞争工作负荷的复杂性。 一般来说，一个“基本”或 S1 SKU 副本可以提供大约 15 QPS，但吞吐量会因为查询的复杂性（多面向查询较为复杂）和网络延迟而或高或低。 此外，必须认识到，尽管添加副本会明显提高规模和性能，但最终效果不一定有线性改善：添加三个副本并不保证带来三倍的吞吐量。

有关的 QPS 详细信息，包括预测工作负荷 QPS 的方法，请参阅[管理搜索服务](search-manage.md)。

## <a name="increase-indexing-performance-with-partitions"></a>使用分区提高索引性能
需要以近乎实时的速度刷新数据的搜索应用程序，需要的分区数在比例上要多于副本。 添加分区可将读/写操作分配到更多的计算资源。 此外，还能提供更多磁盘空间来存储更多的索引和文档。

索引越大，查询所需的时间就越长。 因此，你可能发现，每次增加分区都需要按比例少量增加副本。 查询和查询卷的复杂性影响查询执行的速度。

## <a name="basic-tier-partition-and-replica-combinations"></a>基本层：分区和副本组合
“基本”服务可以包含一个分区以及最多三个副本，上限为三个 SU。 唯一可调整的资源是副本。 至少需要两个副本才能实现查询的高可用性。

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>标准层：分区和副本组合
下表显示了支持副本和分区组合所需的搜索单位数，所有标准层限制为 36 个 SU。

|   | **1 个分区** | **2 个分区** | **3 个分区** | **4 个分区** | **6 个分区** | **12 个分区** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 个副本** |1 个 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 个副本** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 个副本** |3 SU |6 SU |9 SU |12 SU |18 SU |36 个 SU |
| **4 个副本** |4 SU |8 SU |12 SU |16 SU |24 SU |不适用 |
| **5 副本** |5 SU |10 SU |15 SU |20 SU |30 SU |不适用 |
| **6 个副本** |6 SU |12 SU |18 SU |24 SU |36 个 SU |不适用 |
| **12 副本** |12 SU |24 SU |36 个 SU |不适用 |不适用 |不适用 |

Azure 网站上详细说明了 SU、定价和容量。 有关详细信息，请参阅 [Pricing Details](https://azure.microsoft.com/pricing/details/search/)（定价详细信息）。

> [!NOTE]
> 副本数和分区数必须能被 12 整除（具体而言，为 1、2、3、4、6、12）。 这是因为，Azure 搜索将每个索引预先分割为 12 个分片，以便将其平均分散到所有分区。 例如，如果服务有三个分区，而你创建了新索引，则每个分区将包含该索引的四个分片。 Azure 搜索为索引分片的方法属于实现细节，在将来的版本中可能发生变化。 尽管目前的分区数为 12，但请不要料想将来该数字永远都是 12。
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>副本和分区资源的计费公式
计算特定组合所用 SU 数的公式为副本数乘以分区数的积，即 (R X P = SU)。 例如，3 个副本乘以&3; 个分区，按&9; 个 SU 计费。

每个 SU 的成本由层决定。基本层的每个 SU 成本低于标准层。 有关每层的费率，请参阅 [Pricing Details](https://azure.microsoft.com/pricing/details/search/)（定价详细信息）。

