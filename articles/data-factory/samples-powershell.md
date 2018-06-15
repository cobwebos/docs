---
title: 适用于 Azure 数据工厂的 Azure PowerShell 示例 | Microsoft Docs
description: Azure PowerShell 示例 - 这些脚本可帮助你创建和管理数据工厂。
services: data-factory
author: douglaslMS
manager: douglaslMS
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 659005bb01672ea6e63d965f89af23024ba53544
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619484"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>适用于 Azure 数据工厂的 Azure PowerShell 示例

下表包括了适用于 Azure 数据工厂的示例 Azure PowerShell 脚本的链接。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 示例](v1/data-factory-samples.md)。

| |  |
|---|---|
|**复制数据**||
|[将 blob 从 Azure Blob 存储中的一个文件夹复制到另一个文件夹](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本将 blob 从 Azure Blob 存储中的一个文件夹复制到同一 Blob 存储中的另一个文件夹。 |
|[将数据从本地 SQL Server 复制到 Azure Blob 存储](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本可从本地 SQL Server 数据库将数据复制到 Azure Blob 存储。 |
|[批量复制](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 示例脚本将数据从 Azure SQL 数据库中的多个表复制到 Azure SQL 数据仓库。 |
|[增量复制](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 将源中的数据初始完全复制到接收器后，此示例 PowerShell 脚本仅从源数据存储将新的或已更新记录加载到接收器数据存储。 |
|**转换数据**||
|[使用 Spark 群集转换数据](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本通过在 Spark 群集上运行程序来转换数据。 |
|**将 SSIS 包直接迁移到 Azure**||
|[创建 Azure-SSIS 集成运行时](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本预配 Azure-SSIS 集成运行时，以便在 Azure 中运行 SQL Server Integration Services (SSIS) 包。 |



