---
title: "Azure Cosmos DB 的联机备份和还原 | Microsoft Docs"
description: "了解如何在 Azure Cosmos DB 数据库上执行自动备份和还原。"
keywords: "备份和还原、联机备份"
services: cosmos-db
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/28/2017
ms.author: raprasa
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 89dc30c5475786d89554f5ec9e10e555267e6d78
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Azure Cosmos DB 的自动联机备份和还原
Azure Cosmos DB 会定期自动备份所有数据。 自动备份不会影响数据库操作的性能或可用性。 所有备份将存储在另一个存储服务中，针对区域灾难会对这些备份进行全局复制。 如果意外删除了 Cosmos DB 容器并且之后需要数据恢复或灾难恢复解决方案，那么自动备份将是合适的方案。  

本文将首先简要回顾 Cosmos DB 中的数据冗余性和可用性，然后再开始讨论备份。 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Cosmos DB 的高可用性 - 概要
Cosmos DB 旨在进行[全球分布](distribute-data-globally.md) - 允许跨多个 Azure 区域以及策略驱动的故障转移和透明多宿主 API 调整吞吐量规模。 作为一个提供 [99.99% 可用性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 的数据库系统，Cosmos DB 中的所有写入在确认到客户端之前，都会通过本地数据中心内的副本仲裁永久提交到本地磁盘。 请注意，Cosmos DB 的高可用性依赖于本地存储，而不依赖于任何外部存储技术。 此外，如果数据库帐户与多个 Azure 区域关联，那么还将跨其他区域复制写入。 若要调整吞吐量规模并以低延迟访问数据，可以拥有任意数量的与数据库帐户关联的读取区域。 在每个读取区域中，（复制）数据会在副本集中永久保存。  

如下面的关系图中所示，单个 Cosmos DB 容器将[水平分区](partition-data.md)。 在下图中，圆圈表示“分区”，每个分区通过副本集而具有高度可用性。 这是单个 Azure 区域（X 轴表示）内的本地分布。 此外，每个分区（含有其相应的副本集）都会在与数据库帐户关联的多个区域中进行全球分布（例如，此图中的三个区域 - 美国东部、美国西部和印度中部）。 “分区集”是一个包含每个区域（Y 轴表示）中多个数据副本的全球式分布实体。 可将优先级分配到与数据库帐户关联的区域，Cosmos DB 将透明故障转移到下一区域，以防灾难发生。 也可以手动模拟故障转移以测试应用程序的端到端可用性。  

下图阐释了 Cosmos DB 的高程度冗余性。

![Cosmos DB 的高程度冗余性](./media/online-backup-and-restore/redundancy.png)

![Cosmos DB 的高程度冗余性](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>完整、自动、联机备份
糟糕，我删除了容器或数据库！ 使用 Cosmos DB，不仅是数据，还有数据的备份都会高度冗余和具可恢复性，来应对区域灾难。 这些自动备份当前大约每四小时进行一次，并且始终存储最新的 2 个备份。 如果数据意外删除或损坏，请在 8 小时内[联系 Azure 支持人员](https://azure.microsoft.com/support/options/)。 

备份不会影响数据库操作的性能或可用性。 Cosmos DB 会在后台进行备份，不会使用预配的 RU 或影响性能，也不会影响数据库的可用性。 

与存储在 Cosmos DB 中的数据不同，自动备份存储在 Azure Blob 存储服务中。 要保证低延迟/高效上传，备份的屏幕快照将上传到位于与 Cosmos DB 数据库帐户的当前写入区域相同的区域中的 Azure Blob 存储实例。 对于针对区域灾难的恢复，Azure Blob 存储中备份数据的屏幕快照将通过异地冗余存储 (GRS) 再次复制到另一区域。 下图显示在美国西部的远程 Azure Blob 存储帐户中备份整个 Cosmos DB 容器（在此示例中，三个主分区全部位于美国西部），然后将 GRS 复制到美国东部。 

下图阐释了 GRS Azure 存储中所有 Cosmos DB 实体的定期完整备份。

![GRS Azure 存储中所有 Cosmos DB 实体的定期完整备份](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>备份保留期
如上所述，Azure Cosmos DB 每四小时获取一次数据快照，并将每个分区的两个最新快照保留 30 天。 根据我们的符合性规定，将在 90 天后清除快照。

若要保留自己的快照，可以使用 Azure Cosmos DB [数据迁移工具](import-data.md#export-to-json-file)中的“导出到 JSON”选项，计划其他备份。 

## <a name="restoring-a-database-from-an-online-backup"></a>从联机备份还原数据库
如果意外删除了数据库或集合，可以[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)或[联系 Azure 支持](https://azure.microsoft.com/support/options/)，从上一次自动备份中还原数据。 如果由于数据损坏而需要还原数据库，请参阅[处理数据损坏](#handling-data-corruption)，因为你需要采取其他步骤，以防止损坏的数据渗透到备份中。 对于要还原备份的特定快照，Cosmos DB 要求数据在该快照的备份周期的持续时间内可用。

## <a name="handling-data-corruption"></a>处理数据损坏
Azure Cosmos DB 保留系统中每个分区的两个最新备份。 当意外删除容器（文档、图、表的集合）或数据库时，该模型可以很好地处理这种情况，因为它可以恢复其中一个最新版本。 然而，在用户可能引入数据损坏问题的情况下，Azure Cosmos DB 可能不知道数据损坏，并且损坏可能已经渗透到备份中。 一旦检测到损坏情况，用户应删除损坏的容器（集合/图/表），以防止备份被损坏的数据覆盖。 由于上次备份可能是四个小时前，用户可以使用[更改源](change-feed.md)来捕获并存储删除容器之前的最近四小时的数据。

## <a name="next-steps"></a>后续步骤

若要在多个数据中心中复制数据库，请参阅[使用 Cosmos DB 对数据进行全球分布](distribute-data-globally.md)。 

若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。


