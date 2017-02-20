---
title: "使用 Azure .NET SDK 管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何管理 Data Lake Analytics 作业、数据源、用户。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3ed1b4121e8e64b07abaeb1117f2b8a0cfd75406
ms.openlocfilehash: 7b2380e45c62684ed29fe819db7e254b968d55d0


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>使用 Azure .NET SDK 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure .NET SDK 管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。 若要查看使用其他工具的管理主题，请单击上述选项卡选项。

**先决条件**

开始本教程之前，必须具有以下先决条件：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="create-an-azure-resource-group"></a>创建 Azure 资源组
如果尚未创建 Azure 资源组，必须创建一个，这样才能创建 Data Lake Analytics 组件。 以下代码演示如何创建资源组：

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

有关详细信息，请参阅 [Azure 资源组和 Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics)。


## <a name="connect-to-azure-data-lake"></a>连接到 Azure Data Lake
需要以下 Nuget 包：

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


以下代码示例的 Main 方法演示如何连接到 Azure 并初始化 Analytics 帐户和 Store 帐户的 Data Lake 客户端管理对象。

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Data Lake 客户端管理对象
Azure Data Lake SDK 包含客户端管理对象集，从中可以完成大多数编程任务。这些对象位于以下两个命名空间中：
* Mirosoft.Azure.Management.DataLake.Analytics
* Microsot.Azure.Management.DataLake.Store

下表列出了在整篇文章中用作示例的对象及其变量。

| 客户端管理对象                  | 代码变量         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 管理客户端对象：
* DataLakeStoreAccountManagementClient - 用于创建和管理 Data Lake Store。
* DataLakeFileSystemAccountManagementClient - 用于文件系统任务，例如，创建文件夹和文件、上载文件、列出文件、访问 ACL 和凭据，以及添加 Azure 存储 Blob 的链接。

尽管可以从 Data Lake 创建 Azure 存储的链接，但无法访问其内容。 为此，必须使用 Azure 存储 SDK API。 不过，可以针对 Azure 存储 Blob 运行 U-SQL 脚本。

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 管理客户端对象：
* DataLakeAnaylyticsAccountManagementClient - 用于创建和管理 Data Lake Analytics 帐户。
* DataLakeAnalyticsCatalogManagementClient - 用于配置 SQL 数据库，包括列出架构。
* DataLakeAnalyticsJobManagementClient - 用于创建和管理 U-SQL 作业。

## <a name="create-accounts"></a>创建帐户
运行任何 Data Lake Analytics 作业之前，必须具有 Data Lake Analytics 帐户。 与 Azure HDInsight 不同，Analytics 帐户未运行作业时无需付费。  只需在其运行作业时付费。  有关详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

另外，每个 Data Lake Analytics 帐户至少需要一个 Data Lake Store 帐户。
  
### <a name="create-a-data-lake-store-account"></a>创建 Data Lake Store 帐户
以下代码演示如何创建 Data Lake Store 帐户。 使用 Create 方法之前，必须通过指定位置来定义其参数。

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>创建 Data Lake Analytics 帐户
以下代码演示如何创建 Data Lake Analytics 帐户。 DataLakeAnalyticsAccountManagementClient 对象的 Create 方法使用 Data Lake Store 帐户的集合作为其参数之一。 必须在此集合中填充 DataLakeStoreAccountInfo 对象的实例。 在此示例中，这些 DataLakeStoreAccountInfo 对象是从一个帮助器方法 (AdlaFromAdlsStoreAccounts) 获取的。 此外，订阅中的 Data Lake Store 帐户并非都要在一个 Data Lake Analytics 帐户中，因此，该代码将会根据已批准的列表检查名称。

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>管理帐户

### <a name="list-data-lake-store-and-analytic-accounts"></a>列出 Data Lake Store 和 Analytic 帐户
以下代码列出订阅中的 Data Lake Store 帐户。 列出操作并不总会提供某个对象的所有属性，在某些情况下，需要针对该对象执行 Get 操作。
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>获取帐户
以下代码使用 DataLakeAnalyticsAccountManagementClient 返回 Data Lake Analytics 帐户（如果该帐户存在）。 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

同样，可以使用 DataLakeStoreAccountManagementClient (_adlsClient) 以相同的方式获取 Data Lake Store 帐户。        
### <a name="delete-an-account"></a>删除帐户
以下代码删除 Data Lake Analytics 帐户（如果存在）。 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

也可以使用 DataLakeStoreAccountManagementClient 以相同的方式删除 Data Lake Store 帐户。

### <a name="get-the-default-data-lake-store-account"></a>获取默认的 Data Lake Store 帐户
每个 Data Lake Analytics 帐户都需要一个默认的 Data Lake Store 帐户。 使用此代码可以确定 Analytics 帐户的默认 Store 帐户。

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>管理数据源
Data Lake Analytics 当前支持以下数据源：

* [Azure Data Lake 存储](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储](../storage/storage-introduction.md)

创建 Analytics 帐户时，必须指定一个 Azure Data Lake 存储帐户作为默认存储帐户。 默认 Data Lake Store 帐户用于存储作业元数据和作业审核日志。 创建 Analytics 帐户后，可添加其他 Data Lake Storage 帐户，以及 Azure 存储帐户的链接。 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>在 Data Lake 中包含 Azure 存储的链接
可在 Data Lake 环境中创建 Azure 存储 Blob 的链接。 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>列出 Data Lake 数据源
以下代码列出指定的 Data Lake Analytics 帐户的 Data Lake Store 帐户和 Data Lake Storage 帐户（适用于 Azure 存储）。

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>将文件上载到 Data Lake Store 帐户
以下代码使用 DataLakeStoreFileSystemManagementClient 将本地文件上载到 Data Lake Store 帐户中。

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>在 Data Lake Store 帐户中创建文件
除了上载文件外，还可以在 Data Lake Store 帐户中以编程方式轻松创建文件用于分析。 以下代码在 .csv 文件中写入 100 个随机字节数组的前 4 个值。

        MemoryStream azMem = new MemoryStream();
        StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

        for (int i = 0; i < 100; i++)
        {
            byte[] gA = Guid.NewGuid().ToByteArray();
            string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
            sw.WriteLine(dataLine);
        }
        sw.Flush();
        azMem.Position = 0;

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>从 Data Lake Store 帐户复制文件
以下代码演示如何使用 DataLakeFileSystemAccountManagementClient 对象执行文件系统操作。 它将电子表格 (.csv) 文件从 Samples/Data/AmbulanceData 目录复制到计算机上的本地目录。

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>列出 Azure 存储容器
以下代码列出指定的 Azure 存储帐户的容器。

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>验证 Azure 存储帐户路径
以下代码检查某个 Data Lake Analytics 帐户 (analyticsAccountName) 中是否存在某个 Azure 存储帐户 (storageAccntName)，以及该 Azure 存储帐户中是否存在某个容器 (containerName)。 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>管理目录和作业
DataLakeAnalyticsCatalogManagementClient 对象提供用于管理 SQL 数据库（为每个 Azure Data Lake Store 提供）的方法。 DataLakeAnalyticsJobManagementClient 提供使用 U-SQL 脚本来提交和管理数据库中运行的作业的方法。

### <a name="list-databases-and-schemas"></a>列出数据库和架构
在可以列出的多种对象中，最常见的对象是数据库及其架构。 以下代码获取数据库的集合，然后枚举每个数据库的架构。

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
        }
    }

在默认的 master 数据库上运行后，此示例的输出如下所示：

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>列出表列
以下代码演示如何使用 Data Lake Analytics 目录管理客户端访问数据库，以列出指定表中的列。

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>列出失败的作业
以下代码列出有关失败的作业的信息。

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>在 U-SQL 脚本中引用 Azure 存储
以下代码是 U-SQL 脚本的开头部分。 此脚本指定要从 Data Lake Store 帐户中的以下文件读取数据：“/Samples/Data/SearchLog.tsv”

    @searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

若要从链接的 Azure 存储帐户中的 Blob 读取数据，必须使用该 Blob 的完整 URL，其格式如下：

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

例如，如果源文件 (SearchLog.tsv) 存储在“contso_33”存储帐户中名为“samples”的 Blob 容器内，则 FROM 语句的路径为：

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Azure 资源组和 Data Lake Analytics
应用程序通常由许多组件构成，例如 Web 应用、数据库、数据库服务器、存储和第三方服务。 可使用 Azure Resource Manager 以组（称为 Azure 资源组）的方式处理应用程序中的资源。 可以通过单个协调的操作为应用程序部署、更新、监视或删除所有资源。 可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 你可以通过查看整个组的累积费用，明确了解组织的帐单开支。 有关详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。 

Data Lake Analytics 服务可包括以下组件：

* Azure Data Lake Analytics 帐户
* 所需的默认 Azure Data Lake 存储帐户
* 一个或多个 Azure Data Lake Analytics 帐户
* 一个或多个 Azure Data Lake Store 帐户，至少需要一个
* 其他链接的 Azure Data Lake Storage 帐户
* 其他 Azure 存储帐户

可在一个资源管理组下创建所有这些组件，使其更易于管理。

![Azure Data Lake Analytics 帐户和存储](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics 帐户和从属存储帐户必须位于同一个 Azure 数据中心。
但资源管理组可以位于不同的数据中心。  

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [Get started with Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Feb17_HO1-->


