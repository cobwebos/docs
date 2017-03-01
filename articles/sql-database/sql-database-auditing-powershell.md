---
title: "PowerShell：管理 Azure SQL 数据库审核 | Microsoft Docs"
description: "使用 PowerShell 配置 Azure SQL 数据库审核，以跟踪数据库事件并将其写入 Azure 存储帐户中的审核日志。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>使用 PowerShell 配置和管理 SQL 数据库审核

以下部分介绍如何使用 PowerShell 配置和管理审核。 若要使用 Azure 门户配置和管理审核，请参阅[在 Azure 门户中配置审核](sql-database-auditing-portal.md)。 若要使用 REST API 配置和管理审核，请参阅[使用 REST API 配置审核](sql-database-auditing-rest.md)。

有关审核的概述，请参阅 [SQL 数据库审核](sql-database-auditing.md)。

## <a name="powershell-cmdlets"></a>PowerShell cmdlet

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>后续步骤

* 若要使用 Azure 门户配置和管理审核，请参阅[在 Azure 门户中配置数据库审核](sql-database-auditing-portal.md)。 
* 若要使用 PowerShell 配置和管理审核，请参阅[使用 REST API 配置数据库审核](sql-database-auditing-rest.md)。
* 有关审核的概述，请参阅[数据库审核](sql-database-auditing.md)。


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

