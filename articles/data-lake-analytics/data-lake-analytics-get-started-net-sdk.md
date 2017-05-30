---
title: "通过 .NET SDK 开始使用 Azure Data Lake Analytics | Microsoft 文档"
description: "了解如何使用 .NET SDK 创建 Data Lake Analytics 帐户、创建 Data Lake Analytics 作业，以及提交使用 U-SQL 编写的作业。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1dfcbc3d-235d-4074-bc2a-e96def8298b6
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/26/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: cef4219080d58f9f55fb10ce4d96e309b5891735
ms.contentlocale: zh-cn
ms.lasthandoff: 05/23/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-net-sdk"></a>教程：通过使用 .NET SDK 实现 Azure Data Lake Analytics 入门
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure.NET SDK 将 [U-SQL](data-lake-analytics-u-sql-get-started.md) 作业提交到 Data Lake Analytics。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

本教程将开发一个用于提交 U-SQL 作业的 C# 控制台应用程序，该作业可以读取制表符分隔值 (TSV) 文件，并将其转换为逗号分隔值 (CSV) 文件。 

## <a name="prerequisites"></a>先决条件

* **Visual Studio 2015、Visual Studio 2013 Update 4 或安装有 Visual C++ 的 Visual Studio 2012**。
* **用于 .NET 的 Microsoft Azure SDK 2.5 或更高版本**。  可以使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)安装它。
* **一个 Azure Data Lake Analytics 帐户**。 

## <a name="create-a-c-console-application"></a>创建 C# 控制台应用程序

可在公共 Azure Blob 容器中找到示例搜索日志。 在应用程序中，将文件下载到工作站，然后将文件上载到 Data Lake Analytics 帐户的默认 Data Lake Store 帐户。

**创建 U-SQL 脚本**

创建包含以下 U-SQL 脚本的 SampleUSQLScript.usql 文本文件，并将该文件放在 `C:\temp\` 路径中。  该路径将在下一过程所要创建的 .NET 应用程序中硬编码。  

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

此 U-SQL 脚本通过 `Extractors.Tsv()` 读取源数据文件，然后通过 `Outputters.Csv()` 创建 csv 文件。 

在 C# 程序中，需准备 `/Samples/Data/SearchLog.tsv` 文件和 `/Output/` 文件夹。    

**创建应用程序**

1. 打开 Visual Studio。
2. 创建 C# 控制台应用程序。
3. 打开 Nuget 包管理器控制台，然后运行以下命令：
   
        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage
4. 在 Program.cs 中粘贴以下代码：
   
        using System;
        using System.IO;
        using System.Collections.Generic;
        using System.Threading;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.WindowsAzure.Storage.Blob;
   
        namespace SdkSample
        {
          class Program
          {
            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
   
            private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
            private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";
   
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
   
            private static void Main(string[] args)
            {
                string localFolderPath = @"c:\temp\";
   
                // Connect to Azure
                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);
   
                SetupClients(creds, SUBSCRIPTIONID);
   
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

                  WaitForNewline("Job output downloaded. You can now exit.");
            }

            public static ServiceClientCredentials AuthenticateAzure(
                string domainName,
                string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
            {
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                _adlaClient.SubscriptionId = subscriptionId;

                _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
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

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                Console.WriteLine(reason + "\r\nPress ENTER to continue...");

                Console.ReadLine();

                if (!String.IsNullOrWhiteSpace(nextAction))
                    Console.WriteLine(nextAction);
            }

            // List all Data Lake Analytics accounts within the subscription
            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
                for (int i = 0; i < accounts.Count; i++)
                {
                    Console.WriteLine(accounts[i].Name);
                }

                return accounts;
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
          }
        }

5. 按 **F5** 运行应用程序。 输出如下所示：
   
    ![Azure Data Lake Analytics 作业 U-SQL .NET SDK 输出](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)
6. 检查输出文件。  默认路径和文件名为 c:\Temp\SearchLog-from-Data-Lake.csv。

## <a name="see-also"></a>另请参阅
* 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
* 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要着手开发 U-SQL 应用程序，请参阅 [使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅 [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md)（Azure Data Lake Analytics U-SQL 语言入门）和 [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348)（U-SQL 语言参考）。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。
* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。


