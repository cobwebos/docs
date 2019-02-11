---
title: 滚动应用程序升级 - Azure SQL 数据库 | Microsoft Docs
description: 了解如何使用 Azure SQL 数据库的异地复制来支持云应用程序的在线升级。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/29/2019
ms.openlocfilehash: 50f6f114a4d90f48218f751e1649e8694e664491
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295740"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>使用 SQL 数据库活动异地复制管理云应用程序的滚动升级

了解如何使用 SQL 数据库中的[活动异地复制](sql-database-auto-failover-group.md)来启用云应用程序的滚动升级。 由于升级是中断性操作，所以它应成为业务连续性规划和设计的一部分。 本文我们介绍了编排升级过程的两种不同方法，并讨论了每种方法的优点和缺点。 针对本文的目的，我们将使用一个应用程序，该应用程序包含一个连接到作为其数据层的单一数据库的网站。 我们的目标是在不对最终用户体验产生任何显著影响的情况下将版本 1 的应用程序升级到版本 2。

在评估升级选项时应考虑以下因素：

* 升级过程中对应用程序可用性的影响。 应用程序的功能可能被限制或降级多长时间。
* 在升级失败的情况下能否回退。
* 升级过程中发生灾难性错误时的应用程序的漏洞问题。
* 总的费用成本。  该成本包括升级过程使用的额外的冗余成本和临时组件的增量成本。

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>升级依赖于数据库备份进行灾难恢复的应用程序

如果应用程序依赖于自动的数据库备份，并且使用异地还原来实现灾难恢复，那么将它部署到单个 Azure 区域。 为了尽量减少最终用户出现的中断情况，需在该区域创建一个过渡环境，其中包含升级过程中涉及的所有应用程序组件。 下图说明了在升级过程开始前的操作环境。 终结点 `contoso.azurewebsites.net` 表示 Web 应用程序的生产槽。 若要启用回退升级功能，需要使用数据库的完全同步副本创建过渡槽。 以下步骤将创建一个适用于升级的过渡环境：

1. 在同一 Azure 区域中创建辅助数据库。 监视此辅助数据库以查看种子设定过程是否已完成 (1)。
2. 为 Web 应用程序创建名为“过渡”的新部署槽位。 它将在 URL 为 `contoso-staging.azurewebsites.net` 的 DNS 中注册 (2)。

> [!NOTE]
> 请注意，上述准备步骤不会影响生产槽，该槽可以在完全访问模式下运行。
>  

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

完成上述准备步骤后，应用程序就可以进行真正的升级了。 下图说明了升级过程所涉及的步骤。

1. 将主数据库设置为只读模式 (3)。 此模式可以保证在升级过程中 Web 应用程序 (V1) 的生产槽将保持只读模式，从而避免 V1 和 V2 数据库实例之间出现数据分歧。  
2. 使用计划的终止模式断开辅助数据库的连接 (4)。 这会创建主数据库的完全同步独立副本。 将升级该数据库。
3. 将主数据库切换为读写模式，并运行升级脚本 (5)。

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

如果升级成功完成，则现在可以将最终用户切换到应用程序的已升级副本。 该副本现在将成为生产槽。  如下图所示，该切换还涉及更多步骤。

1. 激活在 Web 应用程序的生产槽和过渡槽之间进行的交换操作 (6)。 该操作将切换两个槽的 URL。 现在，`contoso.azurewebsites.net` 将指向网站和数据库的 V2 版本（生产环境）。  
2. 如果不再需要 V1 版本（此版本在交换后已成为一个过渡副本），则可停用过渡环境 (7)。

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

如果升级过程不成功，例如由于升级的脚本中的一个错误，那么过渡槽应被视为已损坏。 若要将应用程序回退到升级前的状态，请将生产槽中的应用程序还原为完全访问模式。 所涉及的步骤如下图所示。

1. 将数据库副本设置为读写模式 (8)。 这可以在功能上还原生产副本的完整 V1。
2. 执行根本原因分析并停用过渡环境 (9)。

此时应用程序可完全正常运行，并且可以重复上述升级步骤。

> [!NOTE]
> 回退操作不需要 DNS 更改，因为尚未执行交换操作。

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

此选项的主要**优点**是可以使用一系列简单步骤升级单个区域中的应用程序。 此升级的费用成本相对较低。 此方法的主要**缺点**在于，如果在升级过程中发生灾难性故障，那么恢复到升级前的状态将涉及在不同的区域重新部署应用程序，并且使用异地还原从备份中还原数据库。 此过程将导致大量的停机时间。

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>升级依赖于数据库异地复制进行灾难恢复的应用程序

如果应用程序利用活动异地复制或故障转移组来实现业务连续性，则会将该应用程序部署到至少两个不同的区域，主要区域包含活动主数据库，备份区域包含只读辅助数据库。 除了前面提到的各个因素，此升级过程还必须保证：

* 在升级过程的任何时候都保护应用程序免受灾难性故障
* 应用程序的地理冗余组件与活动组件一同升级

为了实现这些目标，除了使用 Web 应用部署槽，还需通过包含一个活动终结点和一个备份终结点的故障转移配置文件来使用 Azure 流量管理器 (ATM)。  下图说明了在升级过程开始前的操作环境。 网站 `contoso-1.azurewebsites.net` 和 `contoso-dr.azurewebsites.net` 表示具有完全地理冗余的应用程序的生产环境。 生产环境包括以下组件：

1. 主区域中的 Web 应用程序 `contoso-1.azurewebsites.net` 的生产槽 (1)
2. 主区域中的主数据库 (2) 
3. 备份区域中 Web 应用程序的备用实例 (3)
4. 备份区域中异地复制的辅助数据库 (4)
5. Azure 流量管理器性能配置文件，包含联机终结点 `contoso-1.azurewebsites.net` 和脱机终结点 `contoso-dr.azurewebsites.net`

若要启用回退升级功能，需要使用应用程序的完全同步副本创建过渡环境。 你需要确保在升级过程中发生灾难性故障时应用程序可以快速恢复，另外过渡环境必须也是地理冗余。 需要执行以下步骤来创建适用于升级的过渡环境：

1. 在主区域中部署 Web 应用程序的过渡槽 (6)
2. 在主 Azure 区域中创建辅助数据库 (7)。 配置 Web 应用程序的过渡槽，以便进行连接。 
3. 通过在主区域中复制辅助数据库（称为“链接异地复制”），在备份区域中创建另一地理冗余的辅助数据库 (8)。
3. 在备份区域中部署 Web 应用程序实例的过渡槽 (9)，并将其配置为连接在步骤 (9) 中创建的异地辅助数据库。


> [!NOTE]
> 请注意上述准备步骤不会影响生产槽中的应用程序，应用程序始终可以在读写模式下完全正常地运行。

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

完成上述准备步骤后，就可以将过渡环境用于升级了。 下图显示了升级的步骤。

1. 将生产槽中的主数据库设置为只读模式 (10)。 此模式可以保证在升级过程中生产数据库 (V1) 不会更改，避免 V1 和 V2 数据库实例之间出现数据分歧。  
2. 使用计划的终止模式断开同一区域中的辅助数据库的连接 (11)。 这会创建生产数据库的独立但完全同步的副本。 将升级该数据库。
3. 针对 `contoso-1-staging.azurewebsites.net`、`contoso-dr-staging.azurewebsites.net` 和过渡主数据库运行升级脚本 (12)。 数据库更改会自动复制到过渡辅助数据库 

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

如果升级成功完成，则现在可以将最终用户切换到应用程序的 V2 版本。 下图说明了所涉及的步骤。

1. 激活在主区域 (13) 和备份区域 (14) 的 Web 应用程序的生产槽和过渡槽之间进行的交换操作。 应用程序的 V2 现在变为生产槽，在备份区域中有冗余副本。
2. 如果不再需要 V1 应用程序，可以停用过渡环境（15 和 16）。  

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

如果升级过程不成功（例如由于升级脚本中出现一个错误而导致升级过程不成功），则可认为过渡环境处于不一致状态。 若要将应用程序回退到升级前的状态，请在生产环境中重新使用 V1 版应用程序。 所需步骤如下图所示。

1. 将生产槽中的主数据库副本设置为读写模式 (17)。 它将在功能上还原生产槽中的完整 V1。
2. 执行根本原因分析并修复或删除过渡环境（18 和 19）。

此时应用程序可完全正常运行，并且可以重复上述升级步骤。

> [!NOTE]
> 回退操作不需要 DNS 更改，因为未执行交换操作。

![SQL 数据库异地复制配置。 云灾难恢复。](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

此升级方法的主要**优点**是可以同时升级应用程序及其地理冗余副本，并且不会在升级过程中破坏业务连续性。 此方法的主要**缺点**是它需要每个应用程序组件的双倍冗余，因此会导致更高的费用成本。 它还涉及更复杂的工作流。

## <a name="summary"></a>摘要

本文中所述的两种升级方法具有不同的复杂性和费用成本，但它们都关注于最小化最终用户仅限于执行只读操作的时间。 该时间由升级脚本的持续时间直接定义。 该时间不依赖于数据库大小、所选的服务层、网站配置和你无法轻松控制的其他因素。 所有准备步骤都从升级步骤中分离出来，不影响生产应用程序。 升级脚本的效率是决定升级期间的最终用户体验的关键因素。 因此改进升级的最佳做法是致力于尽可能地提高升级脚本的效率。  

## <a name="next-steps"></a>后续步骤

* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)。
* 若要了解 Azure SQL 数据库活动异地复制，请参阅[使用活动异地复制创建可读辅助数据库](sql-database-active-geo-replication.md)。
* 若要了解 Azure SQL 数据库故障转移组，请参阅[使用自动故障转移组可以实现多个数据库的透明、协调式故障转移](sql-database-auto-failover-group.md)。
* 若要了解 Azure 应用服务中的部署槽和过渡环境，请参阅[在 Azure 应用服务中设置过渡环境](../app-service/deploy-staging-slots.md)。  
* 若要了解 Azure 流量管理器配置文件，请参阅[管理 Azure 流量管理器配置文件](../traffic-manager/traffic-manager-manage-profiles.md)。  


