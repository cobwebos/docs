<properties
    pageTitle="使用 PowerShell 新建 SQL 数据库设置 | Microsoft Azure"
    description="了解如何使用 PowerShell 创建 SQL 数据库。可以通过 PowerShell cmdlet 管理常见的数据库设置任务。"
    keywords="新建 sql 数据库,数据库设置"
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# 使用 PowerShell cmdlet 创建 SQL 数据库，并执行常见的数据库设置任务


> [AZURE.SELECTOR]
- [Azure 门户](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



了解如何通过使用 PowerShell cmdlet 创建 SQL 数据库。（有关如何创建弹性数据库，请参阅[使用 PowerShell 创建新的弹性数据库池](sql-database-elastic-pool-create-powershell.md)。）


[AZURE.INCLUDE [启动 PowerShell 会话](../../includes/sql-database-powershell.md)]

## 数据库设置：创建资源组、服务器和防火墙规则

一旦你已经有了针对所选的 Azure 订阅运行 cmdlet 所需的访问权限，下一步就可以建立一个资源组，使其中包含创建数据库所需的服务器。你可以编辑下一个命令，以便使用所选择的有效位置。运行 **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location**，以获取有效位置列表。

运行以下命令来创建资源组：

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### 创建服务器

SQL 数据库在 Azure SQL 数据库服务器中创建。运行 **New-AzureRmSqlServer** 命令来创建服务器。服务器的名称必须对所有 Azure SQL Database 服务器是唯一的。如果服务器名称已被使用，则会出现错误。还必须指出的是，该命令可能需要数分钟才能运行完毕。可以编辑该命令，以便使用所选择的任何有效位置，但应使用在上一步中创建的资源组使用的相同位置。

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

运行此命令时，系统会提示你输入用户名和密码。不能输入 Azure 凭据。而是输入用户名和密码以服务器管理员的身份来创建。本文底部的脚本显示如何在代码中设置服务器凭据。

成功创建服务器后，会显示服务器详细信息。

### 配置服务器防火墙规则，允许对服务器进行访问

若要访问服务器，需要建立防火墙规则。运行以下命令，将开始和结尾的 IP 地址替换为你计算机的有效值。

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

成功创建规则后，会显示防火墙规则详细信息。

若要允许其他 Azure 服务访问该服务器，请添加一个防火墙规则并将 tartIpAddress 和 EndIpAddress 都设置为 0.0.0.0。该规则会允许来自任何 Azure 订阅的 Azure 流量访问该服务器。

有关详细信息，请参阅 [Azure SQL 数据库防火墙](sql-database-firewall-configure.md)。


## 创建 SQL 数据库

现在，你已经有了一个资源组、一个服务器，并且配置了防火墙规则，因此可以访问服务器。

下面的命令会通过 S1 性能级别在标准服务层创建（空白）SQL 数据库：


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


成功创建数据库后，会显示数据库详细信息。

## 创建 SQL 数据库 PowerShell 脚本

下面的 PowerShell 脚本会创建一个 SQL 数据库和所有相关资源。将 `{variables}` 全部替换成特定于你的订阅和资源的值（设置值时，请删除 **{}**)。

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## 后续步骤
创建 SQL 数据库并执行基本的数据库设置任务后，可以执行以下操作：

- [使用 PowerShell 管理 SQL 数据库](sql-database-command-line-tools.md)
- [使用 SQL Server Management Studio 连接到 SQL 数据库并执行示例 T-SQL 查询](sql-database-connect-query-ssms.md)


## 其他资源

- [Azure SQL 数据库](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0921_2016-->