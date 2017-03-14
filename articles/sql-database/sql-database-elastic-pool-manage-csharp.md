---
title: "C#：创建和管理 Azure SQL 数据库弹性池 | Microsoft Docs"
description: "使用 C# 数据库开发技术来管理 Azure SQL 数据库弹性池。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: f6e6ff3b-6b60-43c1-afe9-575991e38237
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: c83a5aacca6669c4259365fba9f444ecb1b2a6c9
ms.openlocfilehash: 1e7393537f6c9e4d6af522dd05f149403a85559e
ms.lasthandoff: 03/01/2017


---
# <a name="create-and-manage-an-elastic-pool-with-cx23"></a>使用 C# 创建和管理弹性池

本主题介绍如何使用 C# 创建和管理可缩放的[弹性池](sql-database-elastic-pool.md)。 还可以使用 [Azure 门户](https://portal.azure.com/)、[PowerShell](sql-database-elastic-pool-manage-powershell.md) 或 REST API 创建和管理 Azure 弹性池。 此外还可以使用 [Transact-SQL](sql-database-elastic-pool-manage-tsql.md) 创建弹性池和将数据库移入和移出弹性池。

> [!NOTE]
> SQL 数据库的许多新功能仅在使用 [Azure Resource Manager 部署模型](../azure-resource-manager/resource-group-overview.md)时才可用，因此，始终应该使用最新版本的**用于 .NET 的 Azure SQL 数据库管理库（[文档](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)）**。 由于早期版本的[基于经典部署模型的库](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)只能实现向后兼容性，因此我们建议使用较高版本的基于 Resource Manager 的库。
> 

本主题说明了如何使用 C# 通过[适用于 .NET 的 Azure SQL 数据库的库文件](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)创建和管理 Azure SQL 弹性池。 若要创建单一 SQL 数据库，请参阅[使用 C# 通过适用于 .NET 的 SQL 数据库的库文件创建 SQL 数据库](sql-database-get-started-csharp.md)。

适用于 .NET 的 Azure SQL 数据库提供了基于 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 的 API，用于包装[基于 Resource Manager 的 SQL 数据库 REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)。

> [!NOTE]
> SQL 数据库的许多新功能仅在使用 [Azure Resource Manager 部署模型](../azure-resource-manager/resource-group-overview.md)时才可用，因此，始终应该使用最新版本的**用于 .NET 的 Azure SQL 数据库管理库（[文档](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)）**。 由于早期版本的[经典部署模型库](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)只能实现向后兼容性，因此建议使用较高版本的基于 Resource Manager 的库。
> 

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项：

* Azure 订阅。 如果需要 Azure 订阅，只需单击本页顶部的“免费帐户”  ，然后再返回完成本文中的相关操作即可。
* Visual Studio。 如需 Visual Studio 的免费副本，请参阅 [Visual Studio 下载](https://www.visualstudio.com/downloads/download-visual-studio-vs) 页。

## <a name="create-a-console-app-and-install-the-required-libraries"></a>创建控制台应用并安装所需的库
1. 启动 Visual Studio。
2. 单击“文件” > “新建” > “项目”。
3. 创建一个 C# **控制台应用程序** ，并将其命名为： *SqlElasticPoolConsoleApp*

## <a name="create-a-sql-database"></a>创建 SQL 数据库

若要使用 C# 创建 SQL 数据库，请加载所需的管理库（使用 [程序包管理器控制台](http://docs.nuget.org/Consume/Package-Manager-Console)）：

1. 单击“工具” > “NuGet 包管理器” > “包管理器控制台”。
2. 键入 `Install-Package Microsoft.Azure.Management.Sql -Pre` 安装 [Microsoft Azure SQL 管理库](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)。
3. 键入 `Install-Package Microsoft.Azure.Management.ResourceManager -Pre` 安装 [Microsoft Azure Resource Manager 库](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)。
4. 键入 `Install-Package Microsoft.Azure.Common.Authentication -Pre` 安装 [Microsoft Azure 常见身份验证库](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication)。 

> [!NOTE]
> 本文中的示例使用每个 API 请求的同步形式，并会一直阻塞，直到对基础服务的 REST 调用完成。 有可用的异步方法。
>  

## <a name="create-a-sql-elastic-pool---c-example"></a>创建 SQL 弹性池 - C# 示例
以下示例将创建资源组、服务器、防火墙规则、弹性池，然后在池中创建 SQL 数据库。 请参阅[创建用于访问资源的服务主体](#create-a-service-principal-to-access-resources)获取 `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` 变量。

将 **Program.cs** 的内容替换为以下内容，使用应用值更新 `{variables}`（不包括 `{}`）。

```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```

## <a name="create-a-service-principal-to-access-resources"></a>创建服务主体以访问资源
以下 PowerShell 脚本创建 Active Directory (AD) 应用程序和服务主体，我们需要对 C# 应用进行身份验证。 该脚本输出我们需要用于前面 C# 示例的值。 有关详细信息，请参阅 [使用 Azure PowerShell 创建服务主体以访问资源](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




若要完成本文中的步骤，需要准备好以下项：

* 弹性池。 若要创建弹性池，请参阅[使用 C# 创建弹性池](sql-database-elastic-pool-manage-csharp.md)。
* Visual Studio。 如需 Visual Studio 的免费副本，请参阅 [Visual Studio 下载](https://www.visualstudio.com/downloads/download-visual-studio-vs) 页。

## <a name="move-a-database-into-an-elastic-pool"></a>将数据库移入弹性池
可将独立的数据库移入或移出弹性池。  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>列出弹性池中的数据库
若要在弹性池中检索所有数据库，请调用 [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) 方法。

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-an-elastic-pool"></a>更改弹性池的性能设置
检索现有池属性。 修改值并执行 CreateOrUpdate 方法。

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## <a name="latency-of-elastic-pool-operations"></a>弹性池操作延迟
* 更改每个数据库的最小 eDTU 数或每个数据库的最大 eDTU 数通常可在五分钟或更少的时间内完成。
* 更改池大小 (eDTU) 所需的时间取决于池中所有数据库的总大小。 更改平均起来每 100 GB 需要 90 分钟或更短的时间。 例如，如果池中所有数据库的总空间为 200 GB，则更改每个池的池 eDTU 时，预计延迟为 3 小时或更短的时间。

## <a name="additional-resources"></a>其他资源
*  若要详细了解 SQL 数据库客户端应用程序的 SQL 错误代码、数据库连接错误和其他问题，请参阅[错误消息](sql-database-develop-error-messages.md)。
* [Azure 资源管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* 有关弹性池指南的信息，请参阅[何时使用弹性池？](sql-database-elastic-pool-guidance.md)


