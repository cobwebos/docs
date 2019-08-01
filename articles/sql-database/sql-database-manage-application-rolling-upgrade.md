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
ms.date: 02/13/2019
ms.openlocfilehash: 55b23b8d8e03a79aa0806a68306017f89c747760
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567773"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>使用 SQL 数据库活动异地复制管理云应用程序的滚动升级

了解如何使用 Azure SQL 数据库中的[活动异地复制](sql-database-auto-failover-group.md)来启用云应用程序的滚动升级。 由于升级是中断性操作，所以它应成为业务连续性规划和设计的一部分。 本文我们介绍了编排升级过程的两种不同方法，并讨论了每种方法的优点和缺点。 针对本文的目的，我们将参考一个应用程序，该应用程序包含一个连接到作为其数据层的单一数据库的网站。 我们的目标是在不对用户体验产生任何重大影响的情况下将版本 1 (V1) 的应用程序升级到版本 2 (V2)。

评估升级选项时，请考虑以下因素：

* 升级过程中对应用程序可用性的影响，例如应用程序的功能可能被限制或降级多长时间。
* 在升级失败的情况下能否回滚。
* 升级过程中发生灾难性错误时的应用程序的漏洞问题。
* 总的费用成本。 此因素包括升级过程使用的临时组件的额外数据库冗余成本和增量成本。

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>升级依赖于数据库备份进行灾难恢复的应用程序

如果应用程序依赖于自动的数据库备份，并且使用异地还原来实现灾难恢复，那么将它部署到单个 Azure 区域。 为了尽量减少用户出现的中断情况，需在该区域创建一个过渡环境，其中包含升级过程中涉及的所有应用程序组件。 第一张图演示了在升级过程开始之前的操作环境。 终结点 `contoso.azurewebsites.net` 表示 Web 应用的生产环境。 若要回滚升级，必须使用数据库的完全同步副本创建过渡环境。 遵循以下步骤创建一个用于升级的过渡环境：

1. 在同一 Azure 区域中创建辅助数据库。 监视此辅助数据库以查看种子设定过程是否已完成 (1)。
2. 为 Web 应用创建新环境并将其命名为“过渡”。 它将在 URL 为 `contoso-staging.azurewebsites.net` 的 Azure DNS 中注册 (2)。

> [!NOTE]
> 这些准备步骤不会影响生产环境，该环境可以在完全访问模式下正常运行。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

完成准备步骤后，应用程序就可以进行真正的升级了。 下图演示了升级过程所涉及的步骤：

1. 将主数据库设置为只读模式 (3)。 此模式保证在升级过程中 Web 应用 (V1) 的生产环境将保持只读模式，从而避免 V1 和 V2 数据库实例之间出现数据分歧。
2. 使用计划的终止模式断开辅助数据库的连接 (4)。 此操作会创建主数据库的完全同步独立副本。 将升级该数据库。
3. 将辅助数据库切换为读写模式，并运行升级脚本 (5)。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

如果升级成功完成，则现在可将用户切换到应用程序的已升级副本，该副本现在是生产环境。 如下图所示，该切换涉及到其他几个步骤：

1. 激活在 Web 应用的生产环境和过渡环境之间进行的交换操作 (6)。 此操作切换两个环境的 URL。 现在，`contoso.azurewebsites.net` 指向网站和数据库的 V2 版本（生产环境）。 
2. 如果不再需要 V1 版本（此版本在交换后已成为一个过渡副本），可以解除过渡环境 (7)。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

如果升级过程不成功（例如，由于升级脚本中出现错误），可认为过渡环境处于损坏状态。 若要将应用程序回滚到升级前的状态，请将生产环境中的应用程序还原为完全访问模式。 下图显示了还原步骤：

1. 将数据库副本设置为读写模式 (8)。 此操作还原生产副本的完整 V1 功能。
2. 执行根本原因分析并解除过渡环境 (9)。

此时应用程序可完全正常运行，你可以重复上述升级步骤。

> [!NOTE]
> 回滚操作不需要 DNS 更改，因为尚未执行交换操作。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

此选项的主要优点是可以使用一系列简单步骤升级单个区域中的应用程序。 此升级的费用成本相对较低。 

此方法的主要缺点在于，如果在升级过程中发生灾难性故障，那么恢复到升级前的状态将涉及在不同的区域重新部署应用程序，并且使用异地还原从备份中还原数据库。 此过程会导致很长的停机时间。

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>升级依赖于数据库异地复制进行灾难恢复的应用程序

如果应用程序使用活动异地复制或自动故障转移组来实现业务连续性，则会将该应用程序部署到至少两个不同的区域。 主要区域包含活动的主数据库，备份区域包含只读的辅助数据库。 除了本文开头提到的因素以外，升级过程还必须保证：

* 在升级过程的任何时候都保护应用程序免受灾难性故障。
* 应用程序的异地冗余组件与活动组件一同升级。

为了实现这些目标，除了使用 Web 应用环境以外，还需要通过包含一个活动终结点和一个备份终结点的故障转移配置文件来利用 Azure 流量管理器。 下图演示了在升级过程开始之前的操作环境。 网站 `contoso-1.azurewebsites.net` 和 `contoso-dr.azurewebsites.net` 表示具有完全地理冗余的应用程序的生产环境。 生产环境包括以下组件：

* 主要区域中的 Web 应用 `contoso-1.azurewebsites.net` 的生产环境 (1)
* 主要区域中的主数据库 (2)
* 备份区域中 Web 应用的后备实例 (3)
* 备份区域中的异地复制的辅助数据库 (4)
* Azure 流量管理器性能配置文件，包含名为 `contoso-1.azurewebsites.net` 的联机终结点和名为 `contoso-dr.azurewebsites.net` 的脱机终结点

若要回滚升级，必须使用应用程序的完全同步副本创建过渡环境。 需要确保在升级过程中发生灾难性故障时应用程序可以快速恢复，另外过渡环境也必须是异地冗余的。 需要执行以下步骤来创建适用于升级的过渡环境：

1. 在主要区域中部署 Web 应用的过渡环境 (6)。
2. 在主 Azure 区域中创建辅助数据库 (7)。 配置 Web 应用的过渡环境以便与它建立连接。 
3. 通过在主要区域中复制辅助数据库，在备份区域中创建另一异地冗余的辅助数据库。 （此方法称为“链接的异地复制”）(8)。
4. 在备份区域中部署 Web 应用实例的过渡环境 (9)，并将其配置为连接在 (8) 中创建的异地冗余辅助数据库。

> [!NOTE]
> 这些准备步骤不会影响生产环境中的应用程序。 该应用程序将在读写模式下完全正常运行。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

完成准备步骤后，可以升级过渡环境。 下图演示了这些升级步骤：

1. 将生产环境中的主数据库设置为只读模式 (10)。 此模式保证在升级过程中生产数据库 (V1) 不会更改，避免 V1 和 V2 数据库实例之间出现数据分歧。

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. 通过断开辅助数据库的连接来终止异地复制 (11)。 此操作创建生产数据库的独立但完全同步的副本。 将升级该数据库。 以下示例使用了 Transact-SQL，但是也可以使用 [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0)。 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. 针对 `contoso-1-staging.azurewebsites.net`、`contoso-dr-staging.azurewebsites.net` 和过渡主数据库运行升级脚本 (12)。 数据库更改会自动复制到过渡辅助数据库。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

如果升级成功完成，则现在可将用户切换到应用程序的 V2 版本。 下图演示了涉及的步骤：

1. 激活在主要区域 (13) 和备份区域 (14) 的 Web 应用的生产环境和过渡环境之间进行的交换操作。 应用程序的 V2 现在变为生产环境，在备份区域中有冗余副本。
2. 如果不再需要 V1 应用程序（15 和 16），则可以解除过渡环境。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

如果升级过程不成功（例如，由于升级脚本中出现错误），可认为过渡环境处于不一致状态。 若要将应用程序回滚到升级前的状态，请在生产环境中重新使用 V1 应用程序。 所需步骤如下图所示：

1. 将生产环境中的主数据库副本设置为读写模式 (17)。 此操作还原生产环境中的完整 V1 功能。
2. 执行根本原因分析并修复或删除过渡环境（18 和 19）。

此时应用程序可完全正常运行，你可以重复上述升级步骤。

> [!NOTE]
> 回滚操作不需要 DNS 更改，因为尚未执行交换操作。

![可实现云灾难恢复的 SQL 数据库异地复制配置。](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

此升级方法的主要优点是可以同时升级应用程序及其异地冗余副本，并且不会在升级过程中破坏业务连续性。

此方法的主要缺点是它需要每个应用程序组件的双倍冗余，因此会导致更高的成本。 它还涉及更复杂的工作流。

## <a name="summary"></a>总结

本文中所述的两种升级方法具有不同的复杂性和成本，但它们都注重于最小化用户仅限于执行只读操作的时间。 该时间由升级脚本的持续时间直接定义。 该时间不依赖于数据库大小、所选的服务层级、网站配置或你无法轻松控制的其他因素。 所有准备步骤都从升级步骤中分离出来，不影响生产应用程序。 升级脚本的效率是决定升级期间的用户体验的关键因素。 因此，改进体验的最佳做法是将工作重心放在尽可能提高升级脚本的效率上。

## <a name="next-steps"></a>后续步骤

* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)。
* 若要了解 Azure SQL 数据库活动异地复制，请参阅[使用活动异地复制创建可读辅助数据库](sql-database-active-geo-replication.md)。
* 若要了解 Azure SQL 数据库自动故障转移组，请参阅[使用自动故障转移组实现多个数据库的透明、协调式故障转移](sql-database-auto-failover-group.md)。
* 若要了解 Azure 应用服务中的过渡环境，请参阅[在 Azure 应用服务中设置过渡环境](../app-service/deploy-staging-slots.md)。
* 若要了解 Azure 流量管理器配置文件，请参阅[管理 Azure 流量管理器配置文件](../traffic-manager/traffic-manager-manage-profiles.md)。
