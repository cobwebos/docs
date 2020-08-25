---
title: 如何从备份还原 Azure Cosmos DB 数据
description: 本文介绍如何从备份还原 Azure Cosmos DB 数据，如何联系 Azure 支持人员还原数据，以及数据恢复后要执行的步骤。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9956ca0ca9c0957557e7ee74883a75c074ff22f8
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797966"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>如何在 Azure Cosmos DB 中从备份还原数据

如果你意外删除了数据库或容器，则可以提交 [支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 或 [调用 Azure 支持](https://azure.microsoft.com/support/options/) 以从自动联机备份还原数据。 Azure 支持仅适用于选定计划，如 **标准版**、 **开发人员**版和计划高于这些计划。 不适用于基本计划  。 若要了解不同的支持方案，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)页。

若要还原备份的特定快照，Azure Cosmos DB 要求在该快照的备份周期的持续时间内可用。

## <a name="request-a-restore"></a>请求还原

在请求还原之前，应该了解以下详细信息：

* 准备好订阅 ID。

* 应根据数据被意外删除或修改的方式，准备好提供其他信息。 建议提前准备可用的信息，从而尽量减少可能在某些有时效的情况下造成不良影响的来回传输。

* 如果删除了整个 Azure Cosmos DB 帐户，则需要提供删除的帐户的名称。 如果创建了同名的另一个帐户，请与支持团队共享该帐户，因为这有助于确定要选择的正确帐户。 建议为每个已删除的帐户提供不同的支持票证，因为这样可以最大程度地减少对还原状态的混淆。

* 如果删除了一个或多个数据库，应提供 Azure Cosmos 帐户及 Azure Cosmos 数据库名，并指定是否存在同名的新数据库。

* 如果删除了一个或多个容器，应提供 Azure Cosmos 帐户名、数据库名和容器名。 并指定是否存在同名容器。

* 如果意外删除或损坏了数据，则应在 8 小时内联系 [Azure 支持](https://azure.microsoft.com/support/options/)，以便 Azure Cosmos DB 团队帮助你从备份中还原数据。 **在创建用于还原数据的支持请求之前，请确保至少将帐户 [的备份保留期增加](online-backup-and-restore.md) 到7天。最好在此事件的8小时内提高你的保留期。** 这样，Azure Cosmos DB 支持团队将有足够的时间来还原你的帐户。

除了 Azure Cosmos 帐户名、数据库名、容器名以外，还应指定数据可以还原到的时间点。 务必尽量精确，因为这有助于我们确定当时可用的最佳备份。 **指定 UTC 时间也很重要。**

下面的屏幕截图说明如何为容器（集合/图/表）创建通过 Azure 门户还原数据的支持请求。 提供其他详细信息（例如数据类型、还原目的、删除数据的时间），以帮助我们设置请求的优先级。

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="使用 Azure 门户创建备份支持请求":::

## <a name="post-restore-actions"></a>还原后的操作

还原数据后，你会收到有关新帐户名（通常采用 `<original-name>-restored1` 格式）和帐户要还原到的时间的通知。 还原的帐户与原始帐户具有相同的预配吞吐量、索引策略，并且二者位于同一区域。 作为订阅管理员或共同管理员的用户可以查看还原的帐户。

还原数据后，应检查并验证还原的帐户中的数据，确保其中包含你需要的版本。 如果一切正常，应该使用 [Azure Cosmos DB 更改源](change-feed.md)或 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)将数据迁移回原始帐户。

建议在迁移数据之后立即删除容器或数据库。 如果不删除已还原的数据库或容器，它们将在请求单位、存储和流出量方面产生成本。

## <a name="next-steps"></a>后续步骤

接下来，可以通过以下文章学习如何将数据迁移回原始帐户：

* 若要提出还原请求，请联系 Azure 支持，并[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [使用 Cosmos DB 更改源](change-feed.md)将数据移动到 Azure Cosmos DB。

* [使用 Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)将数据移动到 Azure Cosmos DB。
