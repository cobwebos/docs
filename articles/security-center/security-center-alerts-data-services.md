---
title: Azure 安全中心中的数据服务威胁检测 |Microsoft Docs
description: 本文介绍 Azure 安全中心提供的数据服务警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665728"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 安全中心中的数据服务威胁检测

 Azure 安全中心将分析数据存储服务的日志，并在检测到对数据资源的威胁时触发警报。 本文列出了安全中心为以下服务生成的警报：

* [Azure SQL 数据库和 Azure SQL 数据仓库](#data-sql)
* [Azure 存储](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL 数据库和 SQL 数据仓库<a name="data-sql"></a>

适用于 Azure SQL 数据库的高级威胁防护可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。

如果出现可疑数据库活动、潜在漏洞或 SQL 注入攻击，以及异常的数据库访问和查询模式，你将看到警报。

适用于 Azure SQL 数据库和 SQL 的高级威胁防护是高级 SQL 安全功能[（](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)包括 Azure sql 数据库、Azure sql 数据库托管实例、Azure Sql 数据仓库数据库和 Azure 虚拟机上的 sql server）的高级 SQL 安全统一包的高级威胁防护。

有关详细信息，请参阅：

* [如何为 Azure SQL Database 启用高级威胁防护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何为 Azure 虚拟机上的 SQL server 启用高级威胁防护](security-center-iaas-advanced-data.md)
* [针对 SQL 数据库和 SQL 数据仓库的威胁防护警报列表](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure 存储<a name="azure-storage"></a>

适用于存储的高级威胁防护（仅适用于 Blob 存储）检测到访问或利用存储帐户时出现的异常和潜在有害尝试。 这一层保护使你无需成为安全专家，就能解决威胁，并可帮助你管理安全监视系统。

>[!NOTE]
>适用于存储的高级威胁防护当前在 Azure 政府版和主权云区域中不可用。

有关详细信息，请参阅：

* [如何为 Azure 存储启用高级威胁防护](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 存储的威胁防护警报列表](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB 警报由异常和潜在有害尝试访问或利用 Azure Cosmos DB 帐户生成。

有关详细信息，请参阅：

* [Azure Cosmos DB 的高级威胁防护（预览版）](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 的威胁防护警报列表（预览）](alerts-reference.md#alerts-azurecosmos)
