---
title: Azure 网络性能监视器的定价常见问题解答 | Microsoft Docs
description: 常见问题解答 - Azure 网络性能监视器
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 1e7e43dc2e7ed386f8f77fd1ab186d2ff34af405
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Azure 网络性能监视器的定价变化

我们听取了客户的反馈，最近已针对 Azure 中的各种监视服务推出了[新的定价体验](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。 本文以易于阅读的“问答”形式，汇总了与 Azure [网络性能监视器](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) 相关的定价变化。

网络性能监视器包括三个组件：
* [性能监视器](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [服务终结点监视器](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [ExpressRoute 监视器](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

以下各部分介绍了 NPM 组件的定价变化。

## <a name="performance-monitor"></a>性能监视

**在旧模型中如何计收性能监视器的使用费？**

NPM 的计费基于以下两个组件的用量和消耗量：
* **节点**：所有综合事务都在节点上发起和终结。 节点也称为代理或 Microsoft 管理代理。
* **数据**：各种网络测试的结果存储在 Azure Log Analytics 存储库中。

在旧模型中，帐单是根据节点数和生成的数据量计算的。 

**在新模型中如何计收性能监视器的使用费？**

NPM 中的性能监视器功能现在根据以下各项的组合计费： 

* 监视的子网链接
* 数据量

**什么是子网链接？**

性能监视器监视网络中两个或更多个位置之间的连接。 一个子网中的一组节点或代理与另一个子网中的一组节点之间的连接称为子网链接。

**我有两个子网（A 和 B），并且每个子网包含多个代理。性能监视器监视从子网 A 中所有代理到子网 B 中所有代理的连接。是否根据子网间的连接数计费？**

不会。 计费时，从子网 A 到子网 B 的所有连接将统一合并成一个子网链接。 只会计收一个连接的费用。 性能监视器继续监视每个子网中各个代理之间的连接。

**监视子网链接的费用是多少？**

有关监视单个子网链接需要支付的整月成本，请参阅 [Ping Mesh](https://azure.microsoft.com/pricing/details/network-watcher/) 部分。

**如何对性能监视器生成的数据收费？**

Log Analytics 的[定价页](https://azure.microsoft.com/pricing/details/log-analytics/)上的“数据引入”部分中提供了针对引入（将数据上传到 Log Analytics，以及数据处理和编制索引）收取的费用。 [定价页](https://azure.microsoft.com/pricing/details/log-analytics/)上的“数据保留”部分中还提供了针对数据保留（即，在第一个月后，根据客户选项保留数据）收取的费用。


## <a name="expressroute-monitor"></a>ExpressRoute 监视器

**ExpressRoute 监视器的使用费是多少？**

ExpressRoute 监视器的费用根据监视期间生成的数据量计费。 有关详细信息，请参阅“如何对性能监视器生成的数据收费？”

**我使用 ExpressRoute 监视器来监视多条 ExpressRoute 线路。费用是否根据监视的线路数来计收？**

我们不会根据线路数或对等互连类型（例如专用对等互连、Microsoft 对等互连）来计费， 而是根据数据量计费，如前所述。

**当 ExpressRoute 监视单条线路时，生成的数据量有多大？**

当 ExpressRoute 监视专用对等连接时，每月生成的数据量如下：

|百分位数      |数据/月 (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


根据上表，位于第 50 百分位的客户需要支付 192 MB 数据的费用。 根据第一个月的价格 2.30 美元/GB，监视线路产生的费用是 0.43 美元 (192 * 2.30 / 1024)。

**数据量出现差异有哪些原因？**

生成的监视数据量取决于若干因素，例如：
* 代理数。 代理数增加时，容错准确性也会提高。
* 网络中的跃点数。
* 源与目标之间的路径数。

（上表中）位于较高百分位的客户通常从其本地网络中的多个有利位置监视其线路。 此外，多个代理会放置在网络中远离服务提供商边缘路由器的更深层位置。 代理通常放置在数据中心的多个用户站点、分支和机架中。

## <a name="service-endpoint-monitor"></a>服务终结点监视器

**服务终结点监视器的使用费是多少？**

服务终结点监视器的使用费根据以下因素计算：
* 连接数
* 数据量

**什么是连接？**

连接是在整个月份中，从单个代理测试对一个终结点（URL 或网络服务）的访问能力。 例如，从三个代理监视 bing.com 的连接构成了三个连接。

**服务终结点监视器的费用是多少？**

请参阅[连接监视](https://azure.microsoft.com/pricing/details/network-watcher/)部分，了解监视终结点的整月费用。 Log Analytics 的[定价页](https://azure.microsoft.com/pricing/details/log-analytics/)上的“数据引入”部分中提供了数据费用。

## <a name="references"></a>参考

[Log Analytics 定价常见问题解答](https://azure.microsoft.com/pricing/details/log-analytics/) - 常见问题解答部分提供了有关免费层、每个节点的定价和其他定价详细信息。

