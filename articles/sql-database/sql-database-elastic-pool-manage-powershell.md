---
title: "PowerShell：创建和管理 Azure SQL 弹性池 | Microsoft Docs"
description: "了解如何使用 PowerShell 管理弹性池。"
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 61289770-69b9-4ae3-9252-d0e94d709331
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ffcf0f0aa80f0a6b65cbef65e361e4830fcca3ff
ms.lasthandoff: 03/10/2017


---

# <a name="create-and-manage-an-elastic-pool-with-powershell"></a>使用 PowerShell 创建和管理弹性池
本主题介绍了如何使用 PowerShell 创建和管理可缩放的[弹性池](sql-database-elastic-pool.md)。  也可以使用 [Azure 门户](https://portal.azure.com/)、REST API 或 [C#](sql-database-elastic-pool-manage-csharp.md) 创建和管理 Azure 弹性池。 此外还可以使用 [Transact-SQL](sql-database-elastic-pool-manage-tsql.md) 创建弹性池和将数据库移入和移出弹性池。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-elastic-pool"></a>创建弹性池
[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) cmdlet 将创建弹性池。 每个池的 eDTU 值、最小和最大 DTU 受服务器层值（基本、标准或高级）的约束。 请参阅[弹性池和入池数据库的 eDTU 和存储限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)。

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>在弹性池中创建共用数据库
使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) cmdlet 并将 **ElasticPoolName** 参数设置为目标池。 要将现有数据库移动到弹性池，请参阅[将数据库移动到弹性池](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool)。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### <a name="complete-script"></a>完整脚本
此脚本会创建 Azure 资源组和服务器。 出现提示时，提供新服务器的管理员用户名和密码（不是你的 Azure 凭据）。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB

## <a name="create-an-elastic-pool-and-add-multiple-pooled-databases"></a>创建弹性池并添加多个入池数据库
使用门户或每次只能创建一个单一数据库的 PowerShell cmdlet 在弹性池中创建多个数据库可能需要一段时间。 若要自动创建到弹性池中，请参阅 [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。   

## <a name="move-a-database-into-an-elastic-pool"></a>将数据库移入弹性池
使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) 可将数据库移入或移出弹性池。

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-an-elastic-pool"></a>更改弹性池的性能设置
当性能受到影响时，可以更改池的设置以适应增长。 使用 [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511\(v=azure.300\).aspx) cmdlet。 将 -Dtu 参数设置为每个池的 eDTU。 有关该参数可能的值，请参阅 [eDTU 和存储限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)。  

    Set-AzureRmSqlElasticPool -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1” -Dtu 1200 -DatabaseDtuMax 100 -DatabaseDtuMin 50

## <a name="get-the-status-of-pool-operations"></a>获取池操作的状态
创建一个弹性池需要一些时间。 你可以使用 [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812\(v=azure.300\).aspx) cmdlet 跟踪池操作（包括创建和更新）的状态。

    Get-AzureRmSqlElasticPoolActivity -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1”

## <a name="get-the-status-of-moving-a-database-into-and-out-of-an-elastic-pool"></a>获取将数据库移入和移出弹性池的状态
移动数据库需要一些时间。 你可以使用 [Get AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687\(v=azure.300\).aspx) cmdlet 跟踪移动状态。

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>获取弹性池的资源使用情况数据
可以检索的以资源池限制值百分比形式表示的指标：   

| 指标名称 | 说明 |
|:--- |:--- |
| cpu\_% |以池的限制百分比形式表示的平均计算使用率。 |
| 物理\_数据\_读取\_% |以基于池的限制的百分比形式表示的平均 I/O 使用率。 |
| 日志\_编写\_% |以池的限制百分比形式表示的平均写入资源使用率。 |
| DTU\_消耗\_% |以池的 eDTU 限制百分比形式表示的平均 eDTU 使用率 |
| 存储\_% |以池的存储限制百分比形式表示的平均存储使用率。 |
| 工作人员\_% |以基于池的限制的百分比形式表示的最大并发工作线程（请求）数量。 |
| 会话\_% |以基于池的限制的百分比形式表示的最大并发会话（请求）数量。 |
| eDTU_limit |在该时间间隔内该弹性池的当前最大弹性池 DTU 设置。 |
| 存储\_限制 |在该时间间隔内该弹性池的当前最大弹性池存储限制设置（以兆字节为单位）。 |
| eDTU \_使用 |在此时间间隔内池使用的平均 eDTU 数。 |
| 存储\_使用 |在此时间间隔内池使用的平均存储空间（以字节为单位） |

**指标粒度/保持期：**

* 以 5 分钟的粒度返回数据。  
* 数据保留期为 35 天。  

此 cmdlet 和 API 将一次调用中可检索的行数限制为 1000 行（以粒度值为 5 分钟计算，大约为 3 天的数据）。 不过，可以使用不同的开始/结束时间间隔来多次调用此命令，以便检索更多数据

要检索指标：

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  

## <a name="get-resource-usage-data-for-a-database-in-an-elastic-pool"></a>获取弹性池中数据库的资源使用情况数据
这些 API 与当前用来监视单一数据库的资源使用情况的 (V12) API 相同，但存在以下语义差异：检索到的指标表示为针对该池设置的每个数据库的最大 eDTU（或者 CPU 或 IO 等基础指标的等效上限）的百分比。 例如，对于任何此类指标来说，50% 的使用率表示特定资源消耗为父池中该资源的每个数据库上限的 50%。

要检索指标：

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

## <a name="add-an-alert-to-an-elastic-pool-resource"></a>向弹性池资源添加警报
可以向弹性池添加警报规则，以便在弹性池达到设置的使用阈值时，向 [URL 终结点](https://msdn.microsoft.com/library/mt718036.aspx)发送电子邮件通知或警报字符串。 使用 Add-AzureRmMetricAlertRule cmdlet。

> [!IMPORTANT]
> 对弹性池资源利用率的监视存在至少 20 分钟的延迟。 当前不支持为弹性池设置短于 30 分钟的警报。 为弹性池设置的任何时长（PowerShell API 中名为“-WindowSize”的参数）短于 30 分钟的警报可能无法被触发。 请确保为弹性池定义的任何警报的时长不短于 30 分钟 (WindowSize)。
>
>

该示例添加了一个警报，以便在弹性池的 eDTU 消耗超出特定阈值时获取通知。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

## <a name="add-alerts-to-all-databases-in-an-elastic-pool"></a>将警报添加到弹性池中的所有数据库
可以将警报规则添加到弹性池中的所有数据库，以便在资源达到警报设置的使用阈值时，向 [URL 终结点](https://msdn.microsoft.com/library/mt718036.aspx)发送电子邮件通知或警报字符串。

> [!IMPORTANT]
> 对弹性池资源利用率的监视存在至少 20 分钟的延迟。 当前不支持为弹性池设置短于 30 分钟的警报。 为弹性池设置的任何时长（PowerShell API 中名为“-WindowSize”的参数）短于 30 分钟的警报可能无法被触发。 请确保为弹性池定义的任何警报的时长不短于 30 分钟 (WindowSize)。
>
>

该示例向弹性池中的所有数据库添加了一个警报，以便在数据库的 DTU 消耗超出特定阈值时获取通知。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    }

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>在一个订阅的多个池中收集和监视资源使用情况数据
一个订阅中有多个数据库时，单独监视每个弹性池非常麻烦。 取而代之的是可以将 SQL 数据库 PowerShell cmdlet 和 T-SQL 查询结合使用，从多个池及其数据库中收集资源使用情况数据，以便监视和分析资源使用情况。 可以在 GitHub SQL Server 存储库及有关该存储库是什么和如何使用的文档中找到此 powershell 脚本集合的[示例实现](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)。

要使用此示例实现，请按照这些步骤进行操作。

1. 下载[脚本和文档](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)：
2. 修改环境的脚本。 指定在其上托管弹性池的一个或多个服务器。
3. 指定要存储收集的指标的遥测数据库。
4. 自定义脚本以指定脚本的执行持续时间。

在高级别中，脚本执行以下操作：

* 枚举给定的 Azure 订阅 （或指定的服务器列表） 中的所有服务器。
* 为每个服务器运行后台作业。 该作业在固定的时间间隔内循环运行，并收集服务器中所有池的遥测数据。 然后将收集的数据加载到指定的遥测数据库。
* 枚举每个池中的数据库列表，以收集数据库资源使用情况数据。 然后将收集的数据加载到遥测数据库。

可以对遥测数据库中收集的指标值进行分析，以监视弹性池及其中的数据库的运行状况。 该脚本还在遥测数据库中安装预定义的表值函数 (TVF)，以便针对指定的时间窗口汇总指标值。 例如，TVF 的结果可用来显示“指定时间窗口内具有最大 eDTU 使用量的前 N 个弹性池”。 或者，使用诸如 Excel 或 Power BI 这样的分析工具对收集的数据进行查询和分析。

### <a name="example-retrieve-resource-consumption-metrics-for-an-elastic-pool-and-its-databases"></a>示例：检索弹性池及其数据库的资源消耗指标值
该示例将检索指定弹性池及其所有数据库的资源消耗指标值。 收集的数据被格式化并写入到 .csv 格式文件中。 该文件可以使用 Excel 进行浏览。

    $subscriptionId = '<Azure subscription id>'          # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name

    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC

    # Construct the pool resource ID and retrive pool metrics at 5-minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }

    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) {
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name;
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId
          }
          $table = $table += $sx
      }
    }

    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }

    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv

    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv

## <a name="latency-of-elastic-pool-operations"></a>弹性池操作延迟
* 更改每个数据库的最小 eDTU 数或每个数据库的最大 eDTU 数通常可在 5 分钟或更少的时间内完成。
* 更改每个池的 eDTU 数取决于池中所有数据库使用的空间总量。 更改平均起来每 100 GB 需要 90 分钟或更短的时间。 例如，如果池中所有数据库使用的总空间为 200 GB，则更改每个池的池 eDTU 时，预计延迟为 3 小时或更短的时间。

有关这些 PowerShell cmdlet 的参考文档，请参阅：

* [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582\(v=azure.300\).aspx)
* [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403\(v=azure.300\).aspx)
* [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589\(v=azure.300\).aspx)

Stop- cmdlet 表示取消，而不是暂停。 你无法在中途恢复升级，而只能从头开始重新升级。 Stop- cmdlet 将清理并释放所有相应的资源。

## <a name="next-steps"></a>后续步骤
* [创建弹性作业](sql-database-elastic-jobs-overview.md) 弹性作业使你可以根据池中数据库的数目运行 T-SQL 脚本。
* 请参阅[使用 Azure SQL 数据库进行扩展](sql-database-elastic-scale-introduction.md)：使用弹性工具扩展、移动数据、查询或创建事务。

