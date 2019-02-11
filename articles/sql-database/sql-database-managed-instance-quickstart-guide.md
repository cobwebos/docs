---
title: 快速入门 - Azure SQL 数据库托管实例 | Microsoft Docs
description: 了解如何快速开始使用 Azure SQL 数据库 - 托管实例
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468166"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Azure SQL 数据库托管实例入门

[Azure SQL 数据库托管实例](sql-database-managed-instance-index.yml)是 SQL Server 的完全托管式 PaaS 版本，它托管在 Azure 云中并放置在你自己的 VNet 中，具有专用 IP 地址。 本部分介绍如何快速配置和创建托管实例以及迁移数据库。

## <a name="quickstart-overview"></a>快速入门概述

本部分提供了可帮助你快速开始使用托管实例的可用文章的概述。 创建第一个托管实例的最简单方法是使用 [Azure 门户](sql-database-managed-instance-get-started.md)，在其中可以配置所需的参数（用户名/密码、核心数、最大存储），并自动创建 Azure 网络环境，而无需了解网络详细信息和基础结构要求。 只需确保有一个允许创建实例的[订阅类型](sql-database-managed-instance-resource-limits.md#supported-subscription-types)即可。

如果你想要使用自己的网络，或者要自定义网络，请参阅如何为托管实例[配置网络环境](#configure-network-environment)。

创建托管实例时，需要使用以下方法之一连接到该实例：

* 创建 [Azure 虚拟机](sql-database-managed-instance-configure-vm.md)，应在其中安装 SQL Server Management Studio，以及用于访问托管实例所在的同一 VNet 中的子网内的托管实例的其他应用。 VM 不能与托管实例位于同一子网中。
* 在计算机上设置[点到站点连接](sql-database-managed-instance-configure-p2s.md)，以便通过此连接将计算机加入托管实例所在的 VNet，并将托管实例用作网络中的其他任何 SQL Server。

或者，可以从本地网络使用 Express Route 或站点到站点连接，但这些方法不在这些快速入门的讨论范围内。

创建托管实例和配置访问权限时，可以开始迁移本地 SQL Server 或 Azure VM 上的数据库。 请注意，如果要迁移的源数据库中存在一些不受支持的功能，则迁移将会失败。 若要避免失败并检查兼容性，可以安装[数据迁移助手 (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)，用于分析 SQL Server 上的数据库，并找出可能会阻止迁移到托管实例的任何问题，例如，是否存在 FileStream 或多个日志文件。 解决这些问题后，即可将数据库迁移到托管实例。 [数据库实验助手](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/)是另一个有用的工具，它可以记录 SQL Server 上的工作负荷，并在托管实例上重放该工作负荷，以便可以确定在迁移到托管实例时是否会出现任何性能问题。

确保可将数据库迁移到托管实例后，可以使用[本机还原](sql-database-managed-instance-get-started-restore.md)功能通过 Transact-SQL 命令创建数据库的备份、将其上传到 Azure Blob 存储，以及通过 Transact-SQL 命令从 Blob 存储还原数据库。

使用这些快速入门可以在托管实例上快速配置、创建和放置数据库。 在某些情况下，需要自定义或自动化托管实例和所需网络环境的部署。 下面将会介绍这些方案。

## <a name="customizing-network-environment"></a>自定义网络环境

尽管在使用 [Azure 门户](sql-database-managed-instance-get-started.md)创建实例时可以自动配置 VNet/子网，但最好是在开始创建托管实例之前创建 VNet/子网，因为这样可以配置 VNet 和子网的参数。 创建和配置网络环境的最简单方法是使用 [Azure 资源部署](sql-database-managed-instance-create-vnet-subnet.md)模板，这样可以创建并配置实例所要放入到的网络和子网。 只需按下 Azure 资源管理器部署按钮，然后在表单中填充参数即可。 或者，可以使用 [PowerShell 脚本](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)自动创建网络。

如果已有一个可用于部署托管实例的 VNet 和子网，需要确保该 VNet 和子网符合[网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 应使用此 [PowerShell 脚本来验证子网的配置是否正确](sql-database-managed-instance-configure-vnet-subnet.md)。 此脚本不仅可以验证网络并报告问题 – 告知应该更改哪些设置，而且还能让你在 VNet/子网中进行必要的更改。 如果你不想要手动配置 VNet/子网，请运行此脚本；对网络基础结构做出任何重大配置更改后，也应运行此脚本。 若要创建并配置自己的网络，请阅读[托管实例文档](sql-database-managed-instance-connectivity-architecture.md)和[此指南](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)。

## <a name="automating-creation-of-managed-instance"></a>自动创建托管实例

 如果尚未根据上一步骤中所述创建网络环境，Azure 门户可以自动为你创建 – 唯一的缺点在于，它会在环境中配置一些默认参数，而以后无法更改这些参数。 或者，也可以使用 [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)、[结合资源管理器模板的 PowerShell](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) 或 [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/)。

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>在尽量缩短停机时间的情况下迁移到托管实例

参考这些快速入门中的文章可以快速设置托管实例和转移数据库。 但是，使用本机还原时，需要等待数据库完成还原，这可能会导致应用程序出现一段停机时间，尤其是数据库较大时。 若要转移生产数据库，可能需要采用更好的方式进行迁移，以确保尽量缩短迁移所导致的停机时间。 [数据迁移服务](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)以增量方式将源数据库中发生的更改推送至要还原到托管实例的某个数据库中，可以在尽量缩短停机时间的情况下迁移数据库。 这样，便可以在尽量缩短停机时间的前提下，快速将应用程序从源数据库切换到目标数据库。

## <a name="next-steps"></a>后续步骤

* 查看[托管实例支持的功能的概要列表](sql-database-features.md)和[详细信息与已知问题](sql-database-managed-instance-transact-sql-information.md)。 
* 了解[托管实例的技术特征](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。 
* 在[操作指南部分](sql-database-howto-managed-instance.md)查找更高级教程。 
