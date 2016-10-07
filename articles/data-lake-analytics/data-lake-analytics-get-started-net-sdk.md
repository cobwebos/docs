<properties 
   pageTitle="通过使用 .NET SDK 实现 Azure Data Lake Analytics 入门 | Azure" 
   description="了解如何使用 .NET SDK 来创建 Data Lake Store 帐户、创建 Data Lake Analytics 作业以及提交用 U SQL 编写的作业。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/22/2016"
   ms.author="edmaca"/>

# 教程：通过使用 .NET SDK 实现 Azure Data Lake Analytics 入门

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


了解如何使用 Azure .NET SDK 来创建 Azure Data Lake Analytics 帐户，在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定义 Data Lake Analytics 作业，并将作业提交至 Data Lake Analytics 帐户。有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

在本教程中，用户将开发一个包含 U-SQL 脚本的控制台应用程序，它能读取制表符分隔值 (TSV) 文件，并将其转换为逗号分隔值 (CSV) 文件。若要通过其他支持的工具来完成此教程，请单击本节顶部的选项卡。

##先决条件

在开始阅读本教程前，你必须具有：

- **Visual Studio 2015、Visual Studio 2013 Update 4 或安装有 Visual C++ 的 Visual Studio 2012**。
- **用于 .NET 的 Microsoft Azure SDK 2.5 或更高版本**。可以使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)安装它。
- **[用于 Visual Studio 的 Data Lake 工具](http://aka.ms/adltoolsvs)**。
- 创建 Azure Active Directory (AAD) 应用程序，并检索其**客户端 ID**、**租户 ID** 和**密钥**。有关 AAD 应用程序的详细信息和如何获取客户端 ID 的说明，请参阅[使用门户创建 Active Directory 应用程序和服务主体](../resource-group-create-service-principal-portal.md)。创建应用程序并生成密钥后，也可以从门户中获取回复 URI 和密钥。


##创建控制台应用程序

在本教程中，将处理一些搜索日志。搜索日志可以存储在 Data Lake Store 或 Azure Blob 存储中。

已将示例搜索日志复制到公共 Azure Blob 容器中。在应用程序中，将文件下载到你的工作站，然后将文件上传到默认的 Data Lake Store 帐户。

**创建应用程序**

1. 打开 Visual Studio。
2. 创建 C# 控制台应用程序。
3. 打开 Nuget 包管理器控制台，然后运行以下命令：

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
        Install-Package WindowsAzure.Storage

4. 将新文件添加到名称为 **SampleUSQLScript.txt** 的项目，然后粘贴以下 U-SQL 脚本。Data Lake Analytics 作业使用 U-SQL 语言编写而成。若要了解有关 U-SQL 的详细信息，请参阅 [U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 语言参考](http://go.microsoft.com/fwlink/?LinkId=691348)。

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
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	此 U-SQL 脚本通过 **Extractors.Tsv()** 读取源数据文件，然后通过 **Outputters.Csv()** 创建 csv 文件。
    
    在 C# 程序中，需要准备 **/Samples/Data/SearchLog.tsv** 文件和 **/Output/** 文件夹。
	
	对于存储在默认 Data Lake 帐户中的文件而言，使用相对路径更为简单。也可使用绝对路径。例如
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    必须使用绝对路径来访问链接的存储帐户中的文件。链接的 Azure 存储帐户中所储存文件的语法是：
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

	>[AZURE.NOTE] 目前 Azure Data Lake Service 存在一个已知问题。如果示例应用程序中断或遇到错误，你可能需要手动删除该脚本创建的 Data Lake Store 和 Data Lake Analytics 帐户。如果你不熟悉该门户，[使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md) 指南将帮助你入门。
       
5. 在 Program.cs 中粘贴以下代码：

		using Microsoft.Azure.Management.DataLake.Analytics;
		using Microsoft.Azure.Management.DataLake.Analytics.Models;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.Store.Models;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Rest;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System;
		using System.Collections.Generic;
		using System.IO;
		
		namespace SdkSample
		{
		  class Program
		  {
		    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
		    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		    private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
		    private static DataLakeStoreAccountManagementClient _adlsClient;
		    private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
		
		    private static string _adlaAccountName;
		    private static string _adlsAccountName;
		    private static string _resourceGroupName;
		    private static string _location;
		    private static string _tenantId;
		    private static string _subId;
		    private static string _clientId;
		    private static string _clientKey;
		
		    private static void Main(string[] args)
		    {
		      _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
		      _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
		      _resourceGroupName = "<RESOURCE-GROUP>"; // TODO: Replace this value. This resource group should already exist.
		      _location = "East US 2";
		      _tenantId = "<TENANT-ID>";
		      _subId = "<SUBSCRIPTION-ID>";
		      _clientId = "<CLIENT-ID>";
		      _clientKey = "<CLIENT-KEY>";
		
		      string localFolderPath = @"c:\temp"; // TODO: Make sure this exists and contains SampleUSQLScript.txt.
		      var tokenCreds = Authenticate(_tenantId, _clientId, _clientKey);
		
		      SetupClients(tokenCreds, _subId); 
		
		      // Run sample scenarios
		      WaitForNewline("Authenticated.", "Creating NEW accounts.");
		      CreateAccounts();
		      WaitForNewline("Accounts created.", "Preparing the source data file.");
		
		      // Transfer the source file from a public Azure Blob container to Data Lake Store.
		      CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
		      blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
		      UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
		      WaitForNewline("Source data file prepared.", "Submitting a job.");
		
		      // Submit the job
		      Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
		      WaitForNewline("Job submitted.", "Waiting for job completion.");
		
		      // Wait for job completion
		      WaitForJob(jobId);
		      WaitForNewline("Job completed.", "Downloading job output.");
		
		      // Download job output
		      DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
		      WaitForNewline("Job output downloaded.", "Deleting accounts.");
		
		      // Delete accounts
		      _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);
		      _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
		
		      WaitForNewline("Accounts deleted. You can now exit.");
		    }
		
		    // Helper function to show status and wait for user input
		    public static void WaitForNewline(string reason, string nextAction = "")
		    {
		      Console.WriteLine(reason + "\r\nPress ENTER to continue...");
		
		      Console.ReadLine();
		
		      if (!String.IsNullOrWhiteSpace(nextAction))
		        Console.WriteLine(nextAction);
		    }
		
		    public static TokenCredentials Authenticate(string tenantId, string clientId, string clientKey)
		    {
		      var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + _tenantId);
		      var creds = new ClientCredential(_clientId, _clientKey);
		      var tokenAuthResult = authContext.AcquireTokenAsync("https://management.core.windows.net/", creds).Result;
		
		      return new TokenCredentials(tokenAuthResult.AccessToken);
		    }
		
		    public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
		    {
		      _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
		      _adlaClient.SubscriptionId = subscriptionId;
		
		      _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
		
		      _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
		
		      _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
		      _adlsClient.SubscriptionId = subscriptionId;
		
		      _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
		    }
		
		    public static void CreateAccounts()
		    {
		      //ADLS account first, ADLA requires an ADLS account
		      var adlsParameters = new DataLakeStoreAccount(location: _location);
		      _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
		
		      var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
		      var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
		      var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
		      _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
		    }
		
		    public static Guid SubmitJobByPath(string scriptPath, string jobName)
		    {
		      var script = File.ReadAllText(scriptPath);
		
		      var jobId = Guid.NewGuid();
		      var properties = new USqlJobProperties(script);
		      var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
		      var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);
		
		      return jobId;
		    }
		
		    public static JobResult WaitForJob(Guid jobId)
		    {
		      var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
		      while (jobInfo.State != JobState.Ended)
		      {
		        jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
		      }
		      return jobInfo.Result.Value;
		    }
		
		    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
		    {
		      var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
		      var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
		      var uploader = new DataLakeStoreUploader(parameters, frontend);
		      uploader.Execute();
		    }
		
		    public static void DownloadFile(string srcPath, string destPath)
		    {
		      var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
		      var fileStream = new FileStream(destPath, FileMode.Create);
		
		      stream.CopyTo(fileStream);
		      fileStream.Close();
		      stream.Close();
		    }
		  }
		}

6. 按 **F5** 运行应用程序。

## 另请参阅

- 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
- 若要查看更复杂的查询，请参阅[使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
- 若要着手开发 U-SQL 应用程序，请参阅[使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 语言参考](http://go.microsoft.com/fwlink/?LinkId=691348)。
- 有关管理任务，请参阅[使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
- 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

<!---HONumber=AcomDC_0921_2016-->