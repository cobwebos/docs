---
title: 入门内容参考
titleSuffix: Azure SQL Managed Instance
description: '内容参考，可帮助你开始使用 Azure SQL 托管实例。 '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: 7c7268aa361c77f1d466ab7a58b74aa91090dc4b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "84708563"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Azure SQL 托管实例入门
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL 托管实例](sql-managed-instance-paas-overview.md)创建与最新 SQL Server (Enterprise Edition) 数据库引擎几乎完全兼容的数据库，提供一个本机[虚拟网络 (VNet)](../../virtual-network/virtual-networks-overview.md) 实现来解决常见的安全问题，并提供现有 SQL Server 客户惯用的[业务模型](https://azure.microsoft.com/pricing/details/sql-database/)。

在本文中，你将找到向你介绍如何快速配置和创建 SQL 托管实例以及迁移数据库的内容参考。

## <a name="quickstart-overview"></a>快速入门概述

参考以下快速入门可以快速创建 SQL 托管实例、为客户端应用程序配置虚拟机或点到站点 VPN 连接，并使用 `.bak` 文件将数据库还原到新的 SQL 托管实例。

### <a name="configure-environment"></a>配置环境

作为第一步，你需要使用将放置托管实例的网络环境创建第一个 SQL 托管实例，并启用从要执行查询的计算机或虚拟机到 SQL 托管实例的连接。 可以使用下列指南：

- [使用 Azure 门户创建 SQL 托管实例](instance-create-quickstart.md)。 在 Azure 门户中配置所需的参数（用户名/密码、核心数、最大存储量），并自动创建 Azure 网络环境，而无需了解网络详细信息和基础结构要求。 只需确保有一个当前允许创建 SQL 托管实例的[订阅类型](resource-limits.md#supported-subscription-types)即可。 若要使用自己的网络，或者要自定义网络，请参阅[为 Azure SQL 托管实例配置现有虚拟网络](vnet-existing-add-subnet.md)或[为 Azure SQL 托管实例创建虚拟网络](virtual-network-subnet-create-arm-template.md)。
- SQL 托管实例在其自身的不带公共终结点的 VNet 中创建。 若要进行客户端应用程序访问，可“在同一 VNet（不同子网）中创建 VM”，或参考以下快速入门之一“从客户端计算机与 VNet 建立点到站点 VPN 连接” ：
  - 在 SQL 托管实例上启用[公共终结点](public-endpoint-configure.md)，以便从环境直接访问数据。
  - [在 SQL 托管实例 VNet 中创建 Azure 虚拟机](connect-vm-instance-configure.md)以建立客户端应用程序（包括 SQL Server Management Studio）连接。
  - 从装有 SQL Server Management Studio 和其他客户端连接应用程序的客户端计算机[与 SQL 托管实例建立点到站点 VPN 连接](point-to-site-p2s-configure.md)。 这是与 SQL 托管实例及其 VNet 建立连接的两种方法之一。

  > [!NOTE]
  > - 也可以从本地网络使用 Express Route 或站点到站点连接，但这些方法不在这些快速入门的讨论范围内。
  > - 请注意，如果将保持期从 0（无限制保留）更改为任意其他值，“保留”将仅适用于保留值更改后所写入的日志（仍保留在保留值设置为“无限制”的期间所写入的日志，即使启用了前述保留）。

作为手动创建 SQL 托管实例的替代方法，可使用 [PowerShell](scripts/create-configure-managed-instance-powershell.md)、[带资源管理器模板的 PowerShell](scripts/create-powershell-azure-resource-manager-template.md) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) 来编写脚本并自动执行此过程。

### <a name="migrate-your-databases"></a>迁移数据库

创建 SQL 托管实例并配置访问权限后，可以开始迁移 SQL Server 数据库。 如果要迁移的源数据库中存在一些不受支持的功能，则迁移可能会失败。 若要避免失败并检查兼容性，可以使用[数据迁移助手 (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) 来对 SQL Server 上的数据库进行分析并找出可能会阻止迁移到 SQL 托管实例的任何问题，例如，是否存在 [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 或多个日志文件。 解决这些问题后，即可将数据库迁移到 SQL 托管实例。 [数据库实验助手](/sql/dea/database-experimentation-assistant-overview)是另一个有用的工具，它可以记录 SQL Server 上的工作负载，并在 SQL 托管实例上重放该工作负载，以便可以确定在迁移到 SQL 托管实例时是否会出现任何性能问题。

确保可将数据库迁移到 SQL 托管实例后，可以使用本机 SQL Server 还原功能通过 `.bak` 文件将数据库还原到 SQL 托管实例。 可以使用此方法从本地安装的 SQL Server 数据库引擎或 Azure 虚拟机迁移数据库。 请参阅快速入门[从备份还原到 SQL 托管实例](restore-sample-database-quickstart.md)。 此快速入门介绍了如何使用 `RESTORE` Transact-SQL 命令从 Azure Blob 存储中存储的 `.bak` 文件还原。

> [!TIP]
> 若要使用 `BACKUP` Transact-SQL 命令创建 Azure Blob 存储中数据库的备份，请参阅 [SQL Server 备份到 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

参考这些快速入门可以快速创建、配置数据库备份并将其还原到 SQL 托管实例。 在某些情况下，需要自定义或自动化 SQL 托管实例和所需网络环境的部署。 下面将会介绍这些方案。

## <a name="customize-network-environment"></a>自定义网络环境

尽管在使用 [Azure 门户](instance-create-quickstart.md)创建实例时可以自动配置 VNet/子网，但最好是在开始创建 SQL 托管实例中的实例之前创建 VNet/子网，因为这样可以配置 VNet 和子网的参数。 创建和配置网络环境的最简单方法是使用 [Azure 资源部署](virtual-network-subnet-create-arm-template.md)模板，这样可以创建并配置实例所要放入到的网络和子网。 只需按下 Azure 资源管理器部署按钮，然后在表单中填充参数即可。

或者，也可以使用此 [PowerShell 脚本](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)自动创建网络。

如果已有一个可用于部署 SQL 托管实例的 VNet 和子网，需要确保该 VNet 和子网符合[网络要求](connectivity-architecture-overview.md#network-requirements)。 使用此 [PowerShell 脚本来验证子网的配置是否正确](vnet-existing-add-subnet.md)。 此脚本可以验证网络并报告问题，告知应该更改哪些设置，然后让你在 VNet/子网中进行必要的更改。 如果你不想要手动配置 VNet/子网，请运行此脚本。 对网络基础结构做出任何重大配置更改后，也可以运行此脚本。 若要创建并配置自己的网络，请参阅[连接体系结构](connectivity-architecture-overview.md)，以及这篇[有关创建和配置 SQL 托管实例环境的最终指南](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)。

## <a name="migrate-to-a-sql-managed-instance"></a>迁移到 SQL 托管实例

上述快速入门使你能够快速设置 SQL 托管实例，以及使用本机 `RESTORE` 功能移动数据库。 如果想要快速完成概念验证，并验证解决方案在 Azure SQL 数据库托管实例上是否有效，这是一个很好的起点。

但是，若要迁移生产数据库，甚至迁移用于某些性能测试的开发/测试数据库，则需要考虑使用其他技术，例如：

- 性能测试 - 测量源 SQL Server 实例的基线性能指标，并将其与迁移数据库的目标 SQL 托管实例上的性能指标进行比较。 详细了解[性能比较的最佳做法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。
- 联机迁移 - 使用本文所述的本机 `RESTORE` 时，必须等待数据库还原完毕（如果该数据库尚未存储在 Azure Blob 存储中，还要等待复制到其中）。 这会导致应用程序出现一段停机时间，尤其是数据库较大时。 若要移动生产数据库，请使用[数据迁移服务 (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)，它可以在尽量缩短停机时间的情况下迁移数据库。 为实现这种迁移，DMS 会以增量方式将源数据库中发生的更改推送到所要还原的 SQL 托管实例数据库。 这样，便可以在尽量缩短停机时间的前提下，快速将应用程序从源数据库切换到目标数据库。

详细了解[建议的迁移过程](migrate-to-instance-from-sql-server.md)。

## <a name="next-steps"></a>后续步骤

- 查看 [SQL 托管实例支持的功能的概要列表](../database/features-comparison.md)和[详细信息与已知问题](transact-sql-tsql-differences-sql-server.md)。
- 了解 [SQL 托管实例的技术特征](resource-limits.md#service-tier-characteristics)。
- 在[如何使用 SQL 托管实例](how-to-content-reference-guide.md)中查看更深入的操作指南。
- [确定适合本地数据库的 Azure SQL 托管实例 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
