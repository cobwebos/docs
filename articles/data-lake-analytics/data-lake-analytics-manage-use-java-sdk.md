---
title: 使用 Azure Java SDK 管理 Azure Data Lake Analytics
description: 本文介绍如何使用 Azure Java SDK 编写管理 Data Lake Analytics 作业、数据源和用户的应用。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 07830b36-2fe3-4809-a846-129cf67b6a9e
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: b8c7d2ba1c782c3b6ae3034d6a9aab5eb19be954
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813633"
---
# <a name="manage-azure-data-lake-analytics-using-a-java-app"></a>使用 Java 应用管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

本文介绍如何通过使用 Azure Java SDK 编写的应用管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。 

## <a name="prerequisites"></a>先决条件
* Java 开发工具包 (JDK) 8（使用 Java 1.8 版）。
* IntelliJ 或其他合适的 Java 开发环境。 本文档中的说明使用 IntelliJ。
* 创建 Azure Active Directory (AAD) 应用程序，并检索其**客户端 ID**、**租户 ID** 和**密钥**。 有关 AAD 应用程序的详细信息和如何获取客户端 ID 的说明，请参阅 [使用门户创建 Active Directory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。 创建应用程序并生成密钥后，可从门户获取回复 URI 和密钥。

## <a name="authenticating-using-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证

以下代码片段提供了非交互式身份验证的代码，在这种验证方式中，应用程序提供自己的凭据。

## <a name="create-a-java-application"></a>创建 Java 应用程序
1. 打开 IntelliJ，使用“命令行应用”模板创建 Java 项目。
2. 右键单击屏幕左侧的项目并单击“添加框架支持”。 选择 “Maven”，并单击“确定”。
3. 打开新创建的 **"pom.xml"** 文件，并在 **\</版本>** 标记和 **\</项目>** 标记之间添加以下文本段：

```
<dependencies>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-authentication</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.rest</groupId>
        <artifactId>client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-store</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-analytics</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.3.6</version>
        <exclusions>
            <exclusion>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```

转到“文件”>“设置”>“生成”>“执行”>“部署”。 选择“生成工具”>“Maven”>“导入”。 然后选中“自动导入 Maven 项目”。

打开`Main.java`并将现有代码块替换为以下代码：

```java
import com.microsoft.azure.CloudException;
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.datalake.store.*;
import com.microsoft.azure.datalake.store.oauth2.*;
import com.microsoft.azure.management.datalake.analytics.implementation.*;
import com.microsoft.azure.management.datalake.store.*;
import com.microsoft.azure.management.datalake.store.implementation.*;
import com.microsoft.azure.management.datalake.store.models.*;
import com.microsoft.azure.management.datalake.analytics.*;
import com.microsoft.azure.management.datalake.analytics.models.*;
import com.microsoft.rest.credentials.ServiceClientCredentials;

import java.io.*;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.UUID;
import java.util.List;

public class Main {
    private static String _adlsAccountName;
    private static String _adlaAccountName;
    private static String _resourceGroupName;
    private static String _location;

    private static String _tenantId;
    private static String _subscriptionId;
    private static String _clientId;
    private static String _clientSecret;

    private static DataLakeStoreAccountManagementClient _adlsClient;
    private static ADLStoreClient _adlsStoreClient;
    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;

    public static void main(String[] args) throws Exception {
        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
        _location = "East US 2";

        _tenantId = "<TENANT-ID>";
        _subscriptionId =  "<SUBSCRIPTION-ID>";
        _clientId = "<CLIENT-ID>";

        _clientSecret = "<CLIENT-SECRET>";

        String localFolderPath = "C:\\local_path\\";

        // ----------------------------------------
        // Authenticate
        // ----------------------------------------
        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
        SetupClients(creds);

        // ----------------------------------------
        // List Data Lake Store and Analytics accounts that this app can access
        // ----------------------------------------
        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeStoreAccountBasic> adlsListResult = _adlsClient.accounts().list();
        for (DataLakeStoreAccountBasic acct : adlsListResult) {
            System.out.println(acct.name());
        }

        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeAnalyticsAccountBasic> adlaListResult = _adlaClient.accounts().list();
        for (DataLakeAnalyticsAccountBasic acct : adlaListResult) {
            System.out.println(acct.name());
        }
        WaitForNewline("Accounts displayed.", "Creating files.");

        // ----------------------------------------
        // Create a file in Data Lake Store: input1.csv
        // ----------------------------------------
        CreateFile("/input1.csv", "123,abc", true);
        WaitForNewline("File created.", "Submitting a job.");

        // ----------------------------------------
        // Submit a job to Data Lake Analytics
        // ----------------------------------------
        String script = "@input = EXTRACT Row1 string, Row2 string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();";
        UUID jobId = SubmitJobByScript(script, "testJob");
        WaitForNewline("Job submitted.", "Getting job status.");

        // ----------------------------------------
        // Wait for job completion and output job status
        // ----------------------------------------
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        System.out.println("Waiting for job completion.");
        WaitForJob(jobId);
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        WaitForNewline("Job completed.", "Downloading job output.");

        // ----------------------------------------
        // Download job output from Data Lake Store
        // ----------------------------------------
        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
        WaitForNewline("Job output downloaded.", "Deleting file.");

        DeleteFile("/output1.csv");
        WaitForNewline("File deleted.", "Done.");
    }

    public static void SetupClients(ServiceClientCredentials creds) {
        _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
        _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
        _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
        _adlsClient.withSubscriptionId(_subscriptionId);
        _adlaClient.withSubscriptionId(_subscriptionId);

        String authEndpoint = "https://login.microsoftonline.com/" + _tenantId + "/oauth2/token";
        AccessTokenProvider provider = new ClientCredsTokenProvider(authEndpoint, _clientId, _clientSecret);
        _adlsStoreClient = ADLStoreClient.createClient(_adlsAccountName + ".azuredatalakestore.net", provider);
    }

    public static void WaitForNewline(String reason, String nextAction) {
        if (nextAction == null)
            nextAction = "";

        System.out.println(reason + "\r\nPress ENTER to continue...");
        try {
            System.in.read();
        } catch (Exception e) {
        }

        if (!nextAction.isEmpty()) {
            System.out.println(nextAction);
        }
    }

    // Create accounts
    public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
        // Create ADLS account
        CreateDataLakeStoreAccountParameters adlsParameters = new CreateDataLakeStoreAccountParameters();
        adlsParameters.withLocation(_location);

        _adlsClient.accounts().create(_resourceGroupName, _adlsAccountName, adlsParameters);

        // Create ADLA account
        AddDataLakeStoreWithAccountParameters adlsInfo = new AddDataLakeStoreWithAccountParameters();
        adlsInfo.withName(_adlsAccountName);

        List<AddDataLakeStoreWithAccountParameters> adlsInfoList = new ArrayList<>();
        adlsInfoList.add(adlsInfo);

        CreateDataLakeAnalyticsAccountParameters adlaParameters = new CreateDataLakeAnalyticsAccountParameters();
        adlaParameters.withLocation(_location);
        adlaParameters.withDefaultDataLakeStoreAccount(_adlsAccountName);
        adlaParameters.withDataLakeStoreAccounts(adlsInfoList);

        _adlaClient.accounts().create(_resourceGroupName, _adlaAccountName, adlaParameters);
    }

    // Create a file
    public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
        byte[] bytesContents = contents.getBytes();

        ADLFileOutputStream stream = _adlsStoreClient.createFile(path, IfExists.OVERWRITE, "777", force);
        stream.write(bytesContents);
        stream.close();
    }

    // Delete a file
    public static void DeleteFile(String filePath) throws IOException, CloudException {
        _adlsStoreClient.delete(filePath);
    }

    // Download a file
    private static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
        ADLFileInputStream stream = _adlsStoreClient.getReadStream(srcPath);

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
            fileContents = writer.toString();
        }

        pWriter.println(fileContents);
        pWriter.close();
    }

    // Submit a U-SQL job
    private static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
        UUID jobId = java.util.UUID.randomUUID();
        CreateJobProperties properties = new CreateUSqlJobProperties();
        properties.withScript(script);
        CreateJobParameters parameters = new CreateJobParameters();
        parameters.withName(jobName);
        parameters.withType(JobType.USQL);
        parameters.withProperties(properties);

        JobInformation jobInfo = _adlaJobClient.jobs().create(_adlaAccountName, jobId, parameters);

        return jobInfo.jobId();
    }

    // Wait for job completion
    private static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        while (jobInfo.state() != JobState.ENDED) {
            jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        }
        return jobInfo.result();
    }

    // Retrieve job status
    private static String GetJobStatus(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        return jobInfo.state().toString();
    }
}
```

为代码片段中调用的参数提供值：
* `localFolderPath`
* `_adlaAccountName`
* `_adlsAccountName`
* `_resourceGroupName`
* `_tenantId`
* `_subId`
* `_clientId`
* `_clientSecret`

## <a name="next-steps"></a>后续步骤

* 若要了解 U-SQL，请参阅 [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md)（Azure Data Lake Analytics U-SQL 语言入门）和 [U-SQL language reference](https://docs.microsoft.com/u-sql/)（U-SQL 语言参考）。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。
* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。
