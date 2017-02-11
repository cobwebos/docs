---
title: "使用 Data Lake Analytics Java SDK 开发应用程序 | Microsoft Docs"
description: "使用 Azure Data Lake Analytics Java SDK 开发应用程序"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 07830b36-2fe3-4809-a846-129cf67b6a9e
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: bbfe4dc74b9743b89ea6a7a99f5fa17672f3c706
ms.openlocfilehash: 94db8988acacd93518e96a81e177882852379fb0


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-java-sdk"></a>教程：通过使用 Java SDK 实现 Azure Data Lake Analytics 入门
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure Data Lake Analytics Java SDK 来创建 Azure Data Lake 帐户以及执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户和处理作业。 有关 Data Lake 的详细信息，请参阅 [Azure Data Lake Analytics](data-lake-analytics-overview.md)。

在本教程中，你将开发一个 Java 控制台应用程序，其中包含常见的管理任务示例以及创建测试数据和提交作业示例。  若要通过其他支持的工具来完成此教程，请单击本节顶部的选项卡。

## <a name="prerequisites"></a>先决条件
* Java 开发工具包 (JDK) 8（使用 Java 1.8 版）。
* IntelliJ 或其他合适的 Java 开发环境。 此步骤是可选的，但建议执行。 以下说明使用 IntelliJ。
* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **启用 Azure 订阅** 进行 Data Lake Analytics 公共预览。 请参阅[说明](data-lake-analytics-get-started-portal.md)。
* 创建 Azure Active Directory (AAD) 应用程序，并检索其**客户端 ID**、**租户 ID** 和**密钥**。 有关 AAD 应用程序的详细信息和如何获取客户端 ID 的说明，请参阅 [使用门户创建 Active Directory 应用程序和服务主体](../resource-group-create-service-principal-portal.md)。 创建应用程序并生成密钥后，也可以从门户中获取回复 URI 和密钥。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 进行身份验证？
以下代码片段提供了 **非交互式** 身份验证的代码，在这种验证方式中，应用程序提供自己的凭据。

对于本教程，需要授予应用程序权限，才能在 Azure 中创建资源。 就本教程而言， **强烈建议** 仅对 Azure 订阅中的新资源组、未用资源组以及空资源组授予此应用程序的“参与者”权限。

## <a name="create-a-java-application"></a>创建 Java 应用程序
1. 打开 IntelliJ，使用 **命令行应用** 模板创建一个新的 Java 项目。
2. 右键单击屏幕左侧的项目并单击“添加框架支持”。 选择 “Maven”，然后单击“确定”。
3. 打开新创建的 **"pom.xml"** 文件，并在 **\</版本>** 标记和 **\</项目>** 标记之间添加以下文本段：

    >[!NOTE]
    >此步骤是临时的，直到在 Maven 中提供 Azure Data Lake Analytics SDK。 在 Maven 中提供 SDK 后，将更新本文。 以后将在 Maven 中提供所有 SDK 更新。
    >

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-20160513.000802-24</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-runtime</artifactId>
                <version>1.0.0-20160513.000812-28</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.rest</groupId>
                <artifactId>client-runtime</artifactId>
                <version>1.0.0-20160513.000825-29</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-analytics</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>
4. 依次转到“文件”、“设置”、“生成”、“执行”和“部署”。 依次选择“生成工具”、“Maven”和“导入”。 然后单击“自动导入 Maven 项目”。
5. 打开 **Main.java**，将现有代码替换为以下代码。 此外，为代码片段中调用的参数提供值，例如 **localFolderPath****_adlaAccountName****_adlsAccountName** 和 **_resourceGroupName**，并为 **CLIENT-ID** **CLIENT-SECRET****TENANT-ID** 和 **SUBSCRIPTION-ID** 替换占位符。

    此代码贯穿 Data Lake Store 和 Data Lake Analytics 帐户的整个创建过程，在存储中创建文件、运行作业、获取作业状态、下载作业输出，并在最后删除帐户。

   > [!NOTE]
   > 目前 Azure Data Lake Service 存在一个已知问题。  如果示例应用程序中断或遇到错误，你可能需要手动删除该脚本创建的 Data Lake Store 和 Data Lake Analytics 帐户。  如果你不熟悉该门户， [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md) 指南将帮助你入门。
   >
   >

        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.analytics.*;
        import com.microsoft.azure.management.datalake.analytics.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.nio.file.Files;
        import java.nio.file.Paths;
        import java.util.ArrayList;
        import java.util.UUID;
        import java.util.List;

        public class Main {
            private static String _adlsAccountName;
            private static String _adlaAccountName;
            private static String _resourceGroupName;
            private static String _location;

            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;

            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;

            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";

                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";

                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.

                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.

                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);

                // Create Data Lake Store and Analytics accounts
                WaitForNewline("Authenticated.", "Creating NEW accounts.");
                CreateAccounts();
                WaitForNewline("Accounts created.", "Displaying accounts.");

                // List Data Lake Store and Analytics accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
                List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
                for (DataLakeAnalyticsAccount acct : adlaListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Accounts displayed.", "Creating files.");

                // Create a file in Data Lake Store: input1.csv
                // TODO: these change order in the next patch
                byte[] bytesContents = "123,abc".getBytes();
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);

                WaitForNewline("File created.", "Submitting a job.");

                // Submit a job to Data Lake Analytics
                UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();", "testJob");
                WaitForNewline("Job submitted.", "Getting job status.");

                // Wait for job completion and output job status
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                System.out.println("Waiting for job completion.");
                WaitForJob(jobId);
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output from Data Lake Store
                DownloadFile("/output1.csv", localFolderPath + "output1.csv");
                WaitForNewline("Job output downloaded.", "Deleting file.");

                // Delete file from Data Lake Store
                DeleteFile("/output1.csv");
                WaitForNewline("File deleted.", "Deleting account.");

                // Delete account
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
                _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
                WaitForNewline("Account deleted.", "DONE.");
            }

            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
                _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
                _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
                _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
                _adlsClient.setSubscriptionId(_subId);
                _adlaClient.setSubscriptionId(_subId);
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";

                System.out.println(reason + "\r\nPress ENTER to continue...");
                try{System.in.read();}
                catch(Exception e){}

                if (!nextAction.isEmpty())
                {
                    System.out.println(nextAction);
                }
            }

            // Create Data Lake Store and Analytics accounts
            public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
                // Create ADLS account
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);

                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);

                // Create ADLA account
                DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
                adlsInfo.setName(_adlsAccountName);

                DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
                adlsInfo.setProperties(adlsInfoProperties);

                List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
                adlsInfoList.add(adlsInfo);

                DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
                adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
                adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);

                DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
                adlaParameters.setLocation(_location);
                adlaParameters.setName(_adlaAccountName);
                adlaParameters.setProperties(adlaProperties);

                    /* If this line generates an error message like "The deep update for property 'DataLakeStoreAccounts' is not supported", please delete the ADLS and ADLA accounts via the portal and re-run your script. */

                _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
            }

            //todo: this changes in the next version of the API
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }

            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
            }

            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();

                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());

                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();

                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }

                pWriter.println(fileContents);
                pWriter.close();
            }

            // Submit a U-SQL job by providing script contents.
            // Returns the job ID
            public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
                UUID jobId = java.util.UUID.randomUUID();
                USqlJobProperties properties = new USqlJobProperties();
                properties.setScript(script);
                JobInformation parameters = new JobInformation();
                parameters.setName(jobName);
                parameters.setJobId(jobId);
                parameters.setType(JobType.USQL);
                parameters.setProperties(properties);

                JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();

                return jobId;
            }

            // Wait for job completion
            public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                while (jobInfo.getState() != JobState.ENDED)
                {
                    jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
                }
                return jobInfo.getResult();
            }

            // Get job status
            public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                return jobInfo.getState().toValue();
            }
        }

1. 按照提示运行并完成应用程序。

## <a name="see-also"></a>另请参阅
* 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
* 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要着手开发 U-SQL 应用程序，请参阅 [使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅 [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md)（Azure Data Lake Analytics U-SQL 语言入门）和 [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348)（U-SQL 语言参考）。
* 有关管理任务，请参阅 [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。



<!--HONumber=Dec16_HO2-->


