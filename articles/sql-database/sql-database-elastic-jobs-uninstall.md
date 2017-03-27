---
title: "如何卸载弹性数据库作业工具"
description: "如何卸载弹性数据库作业工具"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: sql-database
ms.custom: multiple databases
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: aa438680348748a771f9016ecdcc68372c6c5e86


---
# <a name="uninstall-elastic-database-jobs-components"></a>卸载弹性数据库作业组件
可以使用门户或 PowerShell 卸载**弹性数据库作业**组件。

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>使用 Azure 门户卸载弹性数据库作业组件
1. 打开 [Azure 门户](https://portal.azure.com/)。
2. 导航到包含**弹性数据库作业**组件的订阅，即安装了弹性数据库作业的订阅。
3. 单击“浏览”，然后单击“资源组”。
4. 选择名为“__ElasticDatabaseJob”的资源组。
5. 删除该资源组。

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>使用 PowerShell 卸载弹性数据库作业组件
1. 启动 Microsoft Azure PowerShell 命令窗口，并导航到 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x 文件夹下的 tools 子目录：键入 **cd tools**
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. 执行.\UninstallElasticDatabaseJobs.ps1 PowerShell 脚本。
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

假设组件的安装使用了默认值，则你可以简单地执行以下脚本：

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>后续步骤
若要重新安装弹性数据库作业，请参阅[安装弹性数据库作业服务](sql-database-elastic-jobs-service-installation.md)

有关弹性数据库作业的概述，请参阅[弹性数据库作业概述](sql-database-elastic-jobs-overview.md)。

<!--Image references-->





<!--HONumber=Nov16_HO3-->


