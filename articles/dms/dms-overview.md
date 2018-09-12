---
title: Azure 数据库迁移服务概述 | Microsoft Docs
description: Azure 数据库迁移服务概述，该服务提供从多个数据库源到 Azure 数据平台的无缝迁移。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 09/01/2018
ms.openlocfilehash: d59850b0234912b02b003f4fc8089d76130151ba
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666223"
---
# <a name="what-is-the-azure-database-migration-service"></a>什么是 Azure 数据库迁移服务？
Azure 数据库迁移服务是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并且最小化停机时间。

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>使用熟悉的工具将数据库迁移到 Azure
Azure 数据库迁移服务集成了一些现有工具和服务的功能。 它为客户提供高度可用的综合解决方案。 该服务使用[数据迁移助手](http://aka.ms/dma)生成评估报告，这些报告提供建议以指导你在执行迁移之前完成所需的更改。 你可以自己选择执行所需的修正。 准备好开始迁移过程时，Azure 数据库迁移服务会执行所有必需步骤。 该过程利用了 Microsoft 确定的最佳做法，因此你便可以在启动迁移项目后高枕无忧。

## <a name="regional-availability"></a>区域可用性
Azure 数据库迁移服务目前在以下区域中可用：

![Azure 数据库迁移服务区域可用性](media\overview\dms-regional-availability.png)

> [!NOTE]
> 联机迁移和 SKU 建议功能目前仅在美国中部、美国东部 2 和西欧区域提供。

有关 Azure 数据库迁移服务区域可用性的最新信息，请在 Azure 全球基础结构站点上参阅[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。

## <a name="next-steps"></a>后续步骤
- [使用 Azure 门户创建 Azure 数据库迁移服务的实例](quickstart-create-data-migration-service-portal.md)。
- [将 SQL Server 迁移到 Azure SQL 数据库](tutorial-sql-server-to-azure-sql.md)。
- [使用 Azure 数据库迁移服务的先决条件概述](pre-reqs.md)。
- [有关使用 Azure 数据库迁移服务的常见问题解答](faq.md)。
