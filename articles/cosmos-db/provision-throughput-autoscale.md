---
title: 在自动缩放模式下创建 Azure Cosmos 容器和数据库。
description: 了解好处、用例、以及如何在自动缩放模式下预配 Azure Cosmos 数据库和容器。
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 20b0bcfe5043d4767199c36796fa1123ed779363
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791140"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>创建具有自动缩放吞吐量的 Azure Cosmos 容器和数据库

Azure Cosmos DB 使你可以在数据库和容器上设置标准（手动）或自动缩放预配吞吐量。 本文介绍自动缩放预配吞吐量的好处和用例。 

自动缩放预配吞吐量非常适合于具有可变或不可预测流量模式，并且在高性能和规模方面需要 SLA 的任务关键型工作负载。 

借助自动缩放，Azure Cosmos DB 会基于使用情况自动并即时地缩放数据库或容器的吞吐量 (RU/s)，而不影响工作负载的可用性、延迟、吞吐量或性能。 

## <a name="benefits-of-autoscale"></a>自动缩放的好处

使用自动缩放预配吞吐量配置的 Azure Cosmos 数据库和容器具有以下好处：

* **Simple**：自动缩放消除了通过自定义脚本或手动缩放容量管理 RU/s 的复杂性。 

* 可缩放：数据库和容器根据需要自动缩放预配吞吐量。 客户端连接、应用程序不会中断，对 Azure Cosmos DB SLA 没有影响。

* 经济高效：自动缩放可通过在不使用时进行缩减来优化 RU/s 使用和成本使用。 你只需按小时为工作负载需要的资源付费。 在一个月中的所有小时内，如果你设置了自动缩放最大 RU/秒（Tmax），并使用了66小时或更短时间的 Tmax，则将使用自动缩放功能保存。 若要了解详细信息，请参阅[如何在标准（手动）和自动缩放预配吞吐量](how-to-choose-offer.md)一文中进行选择。

* 高可用性：使用自动缩放的数据库和容器使用相同的全球分布式、容错、高可用 Azure Cosmos DB 后端，以确保数据持续性和高可用性。

## <a name="use-cases-of-autoscale"></a>自动缩放的用例

自动缩放的用例包括：

* 可变或不可预测的工作负载：当工作负载的使用具有可变或不可预测的峰值时，自动缩放会基于使用情况自动增加和缩减。 示例包括根据季节性具有不同流量模式的零售网站；在一天中的不同时间出现峰值的 IOT 工作负载；每月或每年几次出现峰值使用量的业务线应用程序，等等。 利用自动缩放，不再需要为峰值或平均容量进行手动预配。 

* 新应用程序：如果开发新应用程序，但不确定所需的吞吐量 (RU/s)，则可以通过自动缩放轻松地开始进行。 可以从 400 - 4000 RU/s 的自动缩放入口点开始，监视使用情况，并随时间推移确定正确的 RU/s。

* 不常使用的应用程序：如果你的应用程序每天、每周或每个月仅使用几个小时（例如低容量应用程序/网站/博客站点），则自动缩放可调整容量以处理峰值使用量，并在结束时进行缩减。 

* 开发和测试工作负载：如果你或你的团队在工作时间内使用 Azure Cosmos 数据库和容器，但在夜间或周末不需要它们，则自动缩放可通过在不使用时缩减到最小值来节省成本。 

* 计划生产工作负载/查询：如果你有一系列要在空闲时间段运行的计划请求、操作或查询，则可以使用自动缩放轻松完成该操作。 需要运行工作负载时，吞吐量将自动扩展到所需吞吐量，并在以后缩减。 

构建这些问题的自定义解决方案不仅需要大量时间，还会在应用程序的配置或代码中引入复杂性。 自动缩放可立即实现以上方案，无需进行自定义或手动容量缩放。 

## <a name="how-autoscale-provisioned-throughput-works"></a>自动缩放预配吞吐量的工作方式

通过自动缩放配置容器和数据库时，需要指定所需的最大吞吐量 `Tmax`。 Azure Cosmos DB 会缩放吞吐量 `T`，如 `0.1*Tmax <= T <= Tmax`。 例如，如果将最大吞吐量设置为 20,000 RU/s,，则吞吐量将在 2000 到 20,000 RU/s 之间进行缩放。 由于缩放是自动且即时的，因此在任何时间点，都可以无延迟地消耗预配 `Tmax`。 

每小时将向你收取系统在该小时内扩展到的最高吞吐量 `T` 的费用。

自动缩放最大吞吐量 `Tmax` 的入口点从 4000 RU/s 开始，这表示可在 400 - 4000 RU/s 之间进行缩放。 可以按增量 1000 RU/s 设置 `Tmax`，并随时更改值。  

## <a name="enable-autoscale-on-existing-resources"></a>对现有资源启用自动缩放

使用 [Azure 门户](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container)在现有数据库或容器上启用自动缩放。 随时可以在自动缩放与标准（手动）预配吞吐量之间切换。 有关详细信息，请参阅[此文档](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work)。 目前，对于所有 Api，只能使用 Azure 门户对现有资源启用自动缩放。

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> 自动缩放的吞吐量和存储限制

对于 `Tmax` 的任何值，数据库或容器都可以存储总共 `0.01 * Tmax GB`。 达到此存储量之后，将基于新的存储值自动增加最大 RU/s，而不会影响应用程序。 

例如，如果从 50,000 RU/s 的最大 RU/s（在 5000 - 50,000 RU/s 之间缩放）开始，则最多可存储 500 GB 数据。 如果超过 500 GB（例如，存储现在为 600 GB），则新的最大 RU/s 将是 60,000 RU/s（在 6000 - 60,000 RU/s 之间缩放）。

将数据库级别吞吐量与自动缩放结合使用时，可以让前 25 个容器共享 4000 的自动缩放最大 RU/s（在 400 - 4000 RU/s 之间缩放），只要未超过 40 GB 存储即可。 有关详细信息，请参阅[此文档](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container)。

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>比较 – 使用手动与自动缩放吞吐量配置的容器
有关更多详细信息，请参阅有关如何在标准（手动）与自动缩放吞吐量之间进行选择的此[文档](how-to-choose-offer.md)。  

|| 具有标准（手动）吞吐量的容器  | 具有自动缩放吞吐量的容器 |
|---------|---------|---------|
| 预配吞吐量 (RU/s) | 手动预配。 | 基于工作负载使用模式自动并即时缩放。 |
| 请求/操作的速率限制 (429)  | 如果消耗超出预配容量，则可能会发生。 | 如果在设置的自动缩放吞吐量范围内消耗 RU/s，则不会发生。    |
| **容量规划** |  必须执行容量规划，并设置所需的确切吞吐量。 |    系统会自动处理容量规划和容量管理。 |
| **定价** | 使用[按小时费率的标准（手动）RU/s](https://azure.microsoft.com/pricing/details/cosmos-db/)，按小时为手动预配 RU/s 付费。 | 按小时为系统在该小时内扩展到的最高 RU/s 付费。 <br/><br/> 对于单写入区域帐户，使用[按小时费率的自动缩放 RU/s](https://azure.microsoft.com/pricing/details/cosmos-db/)，为每小时使用的 RU/s 付费。 <br/><br/>对于具有多个写入区域的帐户，自动缩放不收取额外费用。 使用相同的[按小时费率的多主数据库 RU/s](https://azure.microsoft.com/pricing/details/cosmos-db/)，为每小时使用的吞吐量付费。 |
| 最适合工作负载类型 |  可预测和稳定的工作负载|   不可预测和可变的工作负载  |

## <a name="next-steps"></a>后续步骤

* 查看[自动缩放常见问题解答](autoscale-faq.md)。
* 了解如何[在手动与自动缩放吞吐量之间进行选择](how-to-choose-offer.md)。
* 了解[如何对 Azure Cosmos 数据库或容器预配自动缩放吞吐量](how-to-provision-autoscale-throughput.md)。
* 深入了解 Azure Cosmos DB 中的[分区](partition-data.md)。


