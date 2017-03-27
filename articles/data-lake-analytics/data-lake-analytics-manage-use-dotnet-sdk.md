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
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>使用 Azure .NET SDK 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure .NET SDK 管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。 若要查看使用其他工具的管理主题，请单击上述选项卡选项。

## <a name="prerequisites"></a>先决条件

* **Visual Studio 2015、Visual Studio 2013 Update 4 或安装有 Visual C++ 的 Visual Studio 2012**。
* **用于 .NET 的 Microsoft Azure SDK 2.5 或更高版本**。  可以使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)安装它。
* **所需的 Nuget 包**

### <a name="install-nuget-packages"></a>安装 Nuget 包
   
   1. 在 Visual Studio 中，右键单击解决方案资源管理器中的项目名称，单击“管理 NuGet 包”。
   2. 在“NuGet 包管理器”选项卡上，确保“包源”设置为“nuget.org”，“包含预发行版”复选框处于选中状态。

   3. 搜索并安装以下 NuGet 包：

    - Microsoft.Rest.ClientRuntime.Azure.Authentication - 本教程使用 V2.2.12
    - Microsoft.Azure.Management.DataLake.Analytics - 本教程使用 V2.1.0 预览版
    - Microsoft.Azure.Management.DataLake.Store - 本教程使用 V2.1.0 预览版

   4. 关闭“NuGet 包管理器” 。

## <a name="client-management-objects"></a>客户端管理对象
Azure Data Lake Analytics 和 Azure Data Lake Store API 包含客户端管理对象集，从中可以完成大多数编程任务。 这些对象位于以下两个命名空间中：
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

下表显示客户端管理对象，其中包含本文整篇代码示例所用的变量。

| 客户端管理对象                  | 代码变量        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 管理客户端对象：
* DataLakeStoreAccountManagementClient - 用于创建和管理 Data Lake Store 帐户。
* DataLakeFileSystemAccountManagementClient - 用于文件系统任务，例如，创建文件夹和文件、上载文件、列出文件、访问 ACL 和凭据，以及添加 Azure 存储 Blob 的链接。

尽管可以从 Data Lake 创建 Azure 存储的链接，但无法访问其内容。 为此，必须使用 Azure 存储 SDK API。 不过，可以针对 Azure 存储 Blob 运行 U-SQL 脚本。

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 管理客户端对象：
* DataLakeAnalyticsAccountManagementClient - 用于创建和管理 Data Lake Analytics 帐户。
* DataLakeAnalyticsCatalogManagementClient - 用于浏览 Data Lake Analytics 中的目录项。
* DataLakeAnalyticsJobManagementClient - 用于提交和管理 Data Lake Analytics 中的作业。

### <a name="example"></a>示例

使用本文下一部分所述的首选身份验证方法获取的凭据初始化客户端管理对象。 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>向 Azure Data Lake Analytics 进行身份验证并连接到它
可以通过多种方法登录到 Azure Data Lake Analytics。

### <a name="interactive-with-provided-credentials"></a>使用提供的凭据进行交互式身份验证
以下代码片段显示由用户提供凭据（例如，用户名和密码或 PIN 号码）进行的最简单身份验证。

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

我们建议在 Azure Active Directory 租户中创建你自己的应用程序和服务主体，然后使用该应用程序的客户端 ID，而不是使用此处使用的示例 ID。

### <a name="non-interactive-with-a-client-secret"></a>使用客户端密码进行非交互式身份验证
可以使用以下代码片段通过客户端密码/应用程序密钥/服务主体对应用程序进行非交互式身份验证。 对现有的 Azure AD“Web 应用”应用程序使用此身份验证选项[](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>使用服务主体进行非交互式身份验证
还可以使用以下代码片段通过应用程序证书/服务主体对应用程序进行非交互式身份验证。 对现有的 Azure AD“Web 应用”应用程序使用此身份验证选项[](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>创建帐户
运行任何 Data Lake Analytics 作业之前，必须具有 Data Lake Analytics 帐户。 另外，每个 Data Lake Analytics 帐户至少需要一个 Data Lake Store 帐户。 有关详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)

### <a name="create-an-azure-resource-group"></a>创建 Azure 资源组
如果尚未创建 Azure 资源组，必须创建一个，这样才能创建 Data Lake Analytics 组件。 需要身份验证凭据、订阅 ID 和位置。 以下代码演示如何创建资源组：

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

有关详细信息，请参阅 [Azure 资源组和 Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics)。


### <a name="create-a-data-lake-store-account"></a>创建 Data Lake Store 帐户
以下代码演示如何创建 Data Lake Store 帐户。 使用 Create 方法之前，必须通过指定位置来定义其参数。

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>创建 Data Lake Analytics 帐户
以下代码演示如何使用异步方法创建 Data Lake Analytics 帐户。 CreateAsync 方法使用 Data Lake Store 帐户的集合作为其参数之一。 必须在此集合中填充 DataLakeStoreAccountInfo 对象的实例。 在此示例中，这些 DataLakeStoreAccountInfo 对象是从一个帮助器方法 (AdlaFromAdlsStoreAccounts) 获取的。

对于任何 Data Lake Analytics 帐户，只需包括执行所需分析所需的 Data Lake Store 帐户。 这些 Data Lake Store 帐户之一必须是默认 Data Lake Store 帐户。

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>管理帐户

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>列出 Data Lake Store 和 Data Lake Analytics 帐户
以下代码列出订阅中的 Data Lake Store 帐户。 列出操作并不总会提供某个对象的所有属性，在某些情况下，需要针对该对象执行 Get 操作。

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>获取帐户
以下代码使用 DataLakeAnalyticsAccountManagementClient 获取 Data Lake Analytics 帐户。 首先进行检查以查看帐户是否存在。

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

同样，可以使用 DataLakeStoreAccountManagementClient (adlsClient) 以相同方式获取 Data Lake Store 帐户。

### <a name="delete-an-account"></a>删除帐户
以下代码删除 Data Lake Analytics 帐户（如果存在）。

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

也可以使用 DataLakeStoreAccountManagementClient 以相同的方式删除 Data Lake Store 帐户。

### <a name="get-the-default-data-lake-store-account"></a>获取默认的 Data Lake Store 帐户
每个 Data Lake Analytics 帐户都需要一个默认的 Data Lake Store 帐户。 使用此代码可以确定 Analytics 帐户的默认 Store 帐户。

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>管理数据源
Data Lake Analytics 当前支持以下数据源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储帐户](../storage/storage-introduction.md)

创建 Analytics 帐户时，必须将一个 Azure Data Lake Store 帐户指定为默认 Data Lake Store 帐户。 默认 Data Lake Store 帐户用于存储作业元数据和作业审核日志。 创建 Analytics 帐户后，可添加其他 Data Lake Store 帐户，以及指向 Azure 存储 (blob) 帐户的链接。

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>从 Data Lake Analytics 帐户链接到 Azure 存储帐户
可以创建指向 Azure 存储帐户的链接。

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>列出 Data Lake Store 数据源
以下代码列出用于指定的 Data Lake Analytics 帐户的 Data Lake Store 帐户和 Azure 存储帐户。

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>上载和下载文件夹和文件
可以通过以下方法使用 Data Lake Store 文件系统客户端管理对象从 Azure 将单个文件或文件夹上载和下载到本地计算机：

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

这些方法的第一个参数是 Data Lake Store 帐户的名称，后跟源路径和目标路径的参数。

以下示例演示如何下载 Data Lake Store 中的文件夹。


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>在 Data Lake Store 帐户中创建文件
可以使用 .NET Framework IO 操作在 Data Lake Store 中创建文件的内容。 以下代码在 .csv 文件中写入 100 个随机字节数组的前 4 个值。

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

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>列出 Azure 存储帐户的 blob 容器
以下代码列出指定的 Azure 存储帐户的容器。

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>验证 Azure 存储帐户路径
以下代码检查某个 Data Lake Analytics 帐户 (analyticsAccountName) 中是否存在某个 Azure 存储帐户 (storageAccntName)，以及该 Azure 存储帐户中是否存在某个容器 (containerName)。

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>管理目录和作业
DataLakeAnalyticsCatalogManagementClient 对象提供用于管理 SQL 数据库（为每个 Azure Data Lake Store 提供）的方法。 DataLakeAnalyticsJobManagementClient 提供使用 U-SQL 脚本来提交和管理数据库中运行的作业的方法。

### <a name="list-databases-and-schemas"></a>列出数据库和架构
在可以列出的多种对象中，最常见的对象是数据库及其架构。 以下代码获取数据库的集合，然后枚举每个数据库的架构。

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
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

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>列出失败的作业
以下代码列出有关失败的作业的信息。

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [Get started with Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

