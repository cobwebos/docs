---
title: Azure Cosmos DB 全球分布的主要优点
description: 了解 Azure Cosmos DB 多主数据库、异地复制提供的主要优点、多主数据库和有用的用例。
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968334"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 在全球范围内分发数据

本文介绍在全球范围内分发数据的主要优点以及需要在全球分发数据的一些实时案例。

## <a name="key-benefits"></a>主要优点

以下主要优点适用于利用 Azure Cosmos DB 全球数据分发功能的客户：

* **单位数延迟** - Azure Cosmos DB 提供第 99 百分位数小于 10 毫秒的读写延迟，由[财政支持 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 担保。

* **5-9 的可用性** -Azure Cosmos DB 提供 99.999% 的读写可用性，由[财政支持 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 担保。

* **灵活的冲突解决** -对于利用多主数据库功能的客户，Azure Cosmos DB 提供三种处理冲突的模式，确保全球数据完整性和一致性。

* **可调整的一致性** - Azure Cosmos DB 除对利用多主数据库功能的客户提供强一致性支持外，还提供 5 种不同的全球分布[一致性级别](consistency-levels.md)支持。

* **隐式容错**  - 通过跨多个区域复制数据，应用程序可享受针对区域中断的高容错性。

## <a name="use-cases"></a>用例

此处只是一小部分方案示例，可充分利用 Azure Cosmos DB 的全球分布功能。

* **社交媒体应用** - 社交媒体应用要求具有较低的延迟性、高可用性和高度可缩放性，以便为全球用户提供卓越的体验。

* **IoT**  - 异地分布式边缘部署通常需要从多个位置跟踪时序数据。 每个设备都可作为其最近区域的宿主设备。 设备移动到其他位置时，这些设备可重新作为宿主设备，以便写入到最近的可用区域。

* **电子商务**  - 电子商务要求具有极低的延迟性和高可用性。 具有读写权限的数据地理分布可将数据放到离用户最近的区域，从而提高应用程序的响应力。 在多个区域的读写可用性提供了更大的可用性。

* **欺诈/异常检测** - 监视用户活动或帐户活动的应用程序通常分布在全球，必须同时跟踪多个事件才能更新得分以保持风险指标内联。

* **计量**  - 使用 Azure Cosmos DB 多主数据库能够轻松地在全球实现用量（例如 API 调用数、每秒事务数，以及使用的分钟数）统计和调控。 内置冲突解决方案确保实时统计和调控的准确性。

## <a name="next-steps"></a>后续步骤  

在本文中，你学习了 Azure Cosmos DB 全球数据分发功能的主要优点和用例。 接下来请浏览以下资源：

* [Azure Cosmos DB 如何启用统包式全球分布](distribute-data-globally-turnkey.md)

* [如何配置 Azure Cosmos DB 全球数据库复制](tutorial-global-distribution-sql-api.md)

* [如何为 Azure Cosmos DB 帐户启用多主数据库](enable-multi-master.md)

* [Azure Cosmos DB 中的自动和手动故障转移的工作原理](regional-failover.md)

* [了解 Azure Cosmos DB 中的冲突解决](multi-master-conflict-resolution.md)

* [将多主数据库与开放源代码 NoSQL 数据库结合使用](multi-master-oss-nosql.md)
