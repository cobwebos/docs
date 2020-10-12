---
title: 适用于 Azure 数据工厂的 Azure PowerShell 示例
description: Azure PowerShell 示例 - 这些脚本可帮助你创建和管理数据工厂。
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 6df400e7ce4b66a1589a0c44089148b33ab88638
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439055"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>适用于 Azure 数据工厂的 Azure PowerShell 示例

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

下表包括了适用于 Azure 数据工厂的示例 Azure PowerShell 脚本的链接。

| Script | 说明  |
|---|---|
|**复制数据**||
|[将 blob 从 Azure Blob 存储中的一个文件夹复制到另一个文件夹](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本将 blob 从 Azure Blob 存储中的一个文件夹复制到同一 Blob 存储中的另一个文件夹。 |
|[将数据从 SQL Server 复制到 Azure Blob 存储](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本将数据从 SQL Server 数据库复制到 Azure Blob 存储。 |
|[大容量复制](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 示例脚本将数据从 Azure SQL 数据库中某个数据库的多个表复制到 Azure Synapse Analytics（以前称为 SQL 数据仓库）。 |
|[增量复制](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 将源中的数据初始完全复制到接收器后，此示例 PowerShell 脚本仅从源数据存储将新的或已更新记录加载到接收器数据存储。 |
|**转换数据**||
|[使用 Spark 群集转换数据](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本通过在 Spark 群集上运行程序来转换数据。 |
|**将 SSIS 包直接迁移到 Azure**||
|[创建 Azure-SSIS 集成运行时](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本预配 Azure-SSIS 集成运行时，以便在 Azure 中运行 SQL Server Integration Services (SSIS) 包。 |



