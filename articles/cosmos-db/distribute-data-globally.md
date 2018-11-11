---
title: 使用 Azure Cosmos DB 全局分布数据 | Microsoft Docs
description: 了解如何通过 Azure Cosmos DB（一种全局分布式多模型数据库服务），使用全局数据库进行全球范围的异地复制、多主数据库、故障转移和数据恢复。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238268"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 全局分布数据

当今的许多应用程序都在全球运行或者有此雄心。 它们必须始终打开，可供全球用户访问。 要管理这些应用程序使用的数据的全局分布，同时提供高性能和高可用性，确实很有难度。 Cosmos DB 是为了解决这些难题而从无到有设计出来的全局分布式数据库服务。

Cosmos DB 是一个基础 Azure 服务，默认可在所有 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中使用。 Microsoft 在全球 50 多个区域运营 Azure 数据中心，并在持续扩张，以满足不断增长的客户需求。 Microsoft 全天候运转 Cosmos DB 服务，让你将工作重心放在自己的应用程序上。 创建 Cosmos DB 帐户时，需确定要将其部署在哪些区域。

Cosmos DB 客户可将其数据库配置为采用全局分布，并在 1 到 50 多个 Azure 区域中的任意位置推出。 为了降低延迟，应将数据定位在靠近用户的位置，因此，在确定区域数目以及要在哪个区域运行时，决定因素在于应用程序的全球覆盖范围以及用户所处的位置。 Cosmos DB 以透明方式将 Cosmos 帐户中的所有数据复制到所有已配置的区域。 Cosmos DB 提供 Cosmos 数据库和容器的单个系统映像，使应用程序能够在本地读取和写入。 使用 Cosmos DB 随时可以添加或删除与帐户关联的区域。 得益于该服务提供的多宿主功能，无需暂停或重新部署应用程序，它就能持续保持高可用性，并随时提供数据。

## <a name="key-benefits-of-global-distribution"></a>全局分布的重要优势

**轻松生成全局主动-主动应用**：借助多主数据库功能，每个区域都是可写的（此外还是可读的），从而可在全球范围内实现无限弹性写入可伸缩性、99.999% 的读写可用性，并保证在 99% 的时间内，以小于 10 毫秒的延迟为快速读写提供服务。  

使用 Azure Cosmos DB 的多宿主 API，应用程序始终知道最靠近的区域并将请求发送到该区域。 无需进行任何配置更改就能识别最靠近的区域。 在 Cosmos DB 帐户中添加和删除区域时，无需重新部署应用程序，它会持续保持高可用性。

**生成高响应能力的应用**：可以轻松将应用程序设计为在针对数据库选择的所有区域中，以小于 10 毫秒的延迟执行近实时读取和写入。  Azure Cosmos DB 在内部处理区域之间的数据复制，并保证针对 Cosmos 帐户选择的一致性级别。

许多应用程序将受益于多区域（本地）写入功能所带来的性能增强。 某些应用程序（例如需要非常一致性的应用程序）偏向于将所有写入汇集到单个区域。 Cosmos DB 支持单区域和多区域这两种配置。

**生成高度可用的应用**：在多个区域中运行数据库可以提高数据库的可用性。 如果一个区域不可用，其他区域会自动处理应用程序请求。 Azure Cosmos DB 为多区域数据库提供 99.999% 的读取和写入可用性。

**发生区域性服务中断时保持业务连续性**：Azure Cosmos DB 支持区域性服务中断期间的[自动故障转移](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account)。 此外，在发生区域性服务中断期间，Cosmos DB 会继续维持其延迟、可用性、一致性和吞吐量方面的 SLA。 为帮助确保整个应用高度可用，Azure Cosmos DB 提供手动故障转移 API 来模拟区域性服务中断。 使用此 API 可以执行常规的业务连续性演练和应对措施。

**全局读取和写入可伸缩性**：使用多主数据库功能，可以在世界各地弹性缩放读取和写入吞吐量。 多主数据库功能保证应用程序针对 Azure Cosmos DB 数据库或容器配置的吞吐量可在所有区域中实现，并受到[有经济保障的 SLA](https://aka.ms/acdbsla) 的保护。

**多个妥善定义的一致性模型**：Azure Cosmos DB 的复制协议可提供五个妥善定义的、实用且直观的一致性模型，每个模型在一致性与性能之间提供清晰的折衷方案。 使用这些一致性模型可以轻松生成适当的全局分布式应用程序。

## <a id="Next Steps"></a>后续步骤

阅读以下文章详细了解全局分布：

* [全球分布 - 揭秘](global-dist-under-the-hood.md)
* [如何配置多宿主客户端](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [如何在数据库中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [如何为 SQL API 帐户创建自定义冲突解决策略](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)