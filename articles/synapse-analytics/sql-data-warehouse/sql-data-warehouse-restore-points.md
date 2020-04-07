---
title: 用户定义的还原点
description: 如何为 SQL 池创建还原点。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745056"
---
# <a name="user-defined-restore-points"></a>用户定义的还原点

在本文中，您将学习如何使用 PowerShell 和 Azure 门户在 Azure Synapse 分析中为 SQL 池创建新的用户定义的还原点。

## <a name="create-user-defined-restore-points-through-powershell"></a>通过 PowerShell 创建用户定义的还原点

若要创建用户定义的还原点，请使用 [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet。

1. 开始之前，请确保[安装 Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的数据库的订阅。
5. 为数据仓库的即时复制创建还原点。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. 查看所有现有还原点的列表。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>通过 Azure 门户创建用户定义的还原点

还可以通过 Azure 门户创建用户定义的还原点。

1. 登录到 [Azure 门户](https://portal.azure.com/)帐户。

2. 导航到要为其创建还原点的 SQL 池。

3. 从左窗格中选择“概述”****，选择“+ 新建还原点”****。 如果未启用"新建还原点"按钮，请确保 SQL 池未暂停。

    ![新建还原点](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 为用户定义的还原点指定名称，然后单击“应用”****。 用户定义的还原点的默认保留期为七天。

    ![还原点的名称](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>后续步骤

- [还原现有 SQL 池](sql-data-warehouse-restore-active-paused-dw.md)
- [还原已删除的 SQL 池](sql-data-warehouse-restore-deleted-dw.md)
- [从异地备份 SQL 池还原](sql-data-warehouse-restore-from-geo-backup.md)

