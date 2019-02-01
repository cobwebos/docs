---
title: 快速入门 - Azure SQL 数据库中的单一数据库 | Microsoft Docs
description: 了解如何快速开始使用 Azure SQL 数据库中的单一数据库
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
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464801"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>开始使用 Azure SQL 数据库中的单一数据库

Azure SQL 数据库中的[单一数据库](sql-database-single-index.yml)是完全托管的 PaaS 数据库即服务 (DbaaS)，也是新式云原生应用程序的理想存储引擎。 本部分介绍如何快速配置和创建 SQL 数据库。

## <a name="quickstart-overview"></a>快速入门概述

本部分提供了可帮助你快速开始使用单一数据库的可用文章的概述。 若要创建第一个 SQL 数据库的最简单方法是使用 [Azure 门户](sql-database-get-started-portal.md)，可在其中配置所需的参数。
创建后，需要[通过配置防火墙规则来保护该数据库](sql-database-get-started-portal-firewall.md)。 

若要将 SQL Server 上的现有数据库迁移到 Azure，应该安装[数据迁移助手 (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)，用于分析 SQL Server 上的数据库，并找出可能会阻止迁移到单一数据库的任何问题。 如果找不到任何问题，可将数据库导出为 `.bacpac` 文件，然后[使用 Azure 门户或 SqlPackage 导入](sql-database-import.md)该文件。

使用这些快速入门可以在 Azure 云中快速配置、创建和导入数据库。

## <a name="automating-management-operations"></a>自动化管理操作

使用 Azure 门户可以轻松创建和修改单一数据库。 或者，可以使用 [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) 或 [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md) 来创建数据库。
还可以使用 [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) 或 [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md) 更新单一数据库和缩放资源。

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>在尽量缩短停机时间的情况下迁移到单一数据库

参考这些快速入门中的文章可以使用 `.bacpac` 快速创建数据库或将其导入 Azure。 但是，使用 `.bacpac` 和 `.dacpack` 文件可以快速在不同的 SQL Server 和 Azure SQL 数据库版本（单一数据库、弹性池或托管实例）之间转移数据库，或者在 DevOps 管道中实现持续集成。 但是，此方法不适合用于在尽量缩短停机时间的前提下迁移生产数据库，因为需要等待将源数据库导出为 `.bacpac` 文件，然后将其导入 Azure SQL 数据库，这会导致应用程序出现一段停机时间，尤其是数据库较大时。 若要转移生产数据库，可能需要采用更好的方式进行迁移，以确保尽量缩短迁移所导致的停机时间。 [数据迁移服务](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json)可以在尽量缩短停机时间的前提下迁移数据库。 这样，便可以在尽量缩短停机时间的前提下，快速将应用程序从源数据库切换到目标数据库。

## <a name="next-steps"></a>后续步骤

* 查看 [Azure SQL 数据库支持的功能的概要列表](sql-database-features.md)。 
* 了解如何[使数据库变得更安全](sql-database-security-tutorial.md)。 
* 在[操作指南部分](sql-database-howto-single-database.md)查找更高级教程。 
* 查找在 [PowerShell](sql-database-powershell-samples.md) 和 [Azure CLI](sql-database-cli-samples.md) 中编写的其他示例脚本。 
* 详细了解可用于配置数据库的[管理 API](sql-database-single-databases-manage.md)。 
