---
title: 用于 Azure 流分析的管理 .NET SDK v1.x
description: 流分析管理 .NET SDK 入门。 了解如何设置和运行分析作业。 创建项目、输入、输出和转换。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/06/2017
ms.openlocfilehash: a8f272403a0c6903521acc7cc45935c4e5bc419c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="management-net-sdk-v1x-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK v1.x：使用用于 .NET 的 Azure 流分析 API 设置和运行分析作业
了解如何通过 Management .NET SDK 使用用于 .NET 的流分析 API 设置和运行分析作业。 设置项目、创建输入和输出源、转换，以及开始和停止作业。 就分析作业来说，可以从 Blob 存储或事件中心流式传输数据。

请参阅 [.NET 版流分析 API 的管理参考文档](https://msdn.microsoft.com/library/azure/dn889315.aspx)。

Azure 流分析是一种完全托管的服务，可以在云中通过流式数据进行低延迟、高度可用、可伸缩且复杂的事件处理。 客户可以使用流分析来设置流式处理作业，以便分析数据流并进行近实时分析。  

> [!NOTE]
> 本文中的示例代码仍使用旧版 (1.x) 的 Azure 流分析 Management .NET SDK。 有关使用最新 SDK 版本的示例代码，请参阅[使用用于流分析的 Management .NET SDK](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk)。

## <a name="prerequisites"></a>先决条件
在开始阅读本文前，必须具有：

* 安装 Visual Studio 2017 或 2015。
* 下载并安装 [Azure .NET SDK](https://azure.microsoft.com/downloads/)。
* 在订阅中创建 Azure 资源组。 下面是 Azure PowerShell 脚本示例。 有关 Azure PowerShell 的信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/overview)；  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* 设置作业要连接到的输入源和输出目标。

## <a name="set-up-a-project"></a>设置项目
要使用 .NET 版流分析 API 创建分析作业，请首先设置项目。

1. 创建 Visual Studio C# .NET 控制台应用程序。
2. 在程序包管理器控制台中运行以下命令以安装 NuGet 包。 第一个是 Azure 流分析管理 .NET SDK。 第二个是用于进行身份验证的 Azure Active Directory 客户端。
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
3. 将下面的 **appSettings** 部分添加到 App.config 文件：
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    将 **SubscriptionId** 和 **ActiveDirectoryTenantId** 的值替换为 Azure 订阅 ID 和租户 ID。 可以通过运行以下 Azure PowerShell cmdlet 来获取这些值：

        Get-AzureAccount

4. 在 .csproj 文件中添加以下引用：

        <Reference Include="System.Configuration" />

1. 将以下 **using** 语句添加到项目中的源文件 (Program.cs)：
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. 添加一个身份验证帮助器方法：

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>创建流分析管理客户端
一个 **StreamAnalyticsManagementClient** 对象，用于管理作业和作业组件，例如输入、输出和转换。

将以下代码添加到 **Main** 方法的开头：

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

resourceGroupName 变量的值应该与你在先决条件步骤中创建或选取的资源组的名称相同。

若要自动执行凭据演示方面的作业创建，请参阅[使用 Azure 资源管理器对服务主体进行身份验证](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

本文的剩余部分假定此代码位于 **Main** 方法的开头。

## <a name="create-a-stream-analytics-job"></a>创建流分析作业
下面的代码会在你所定义的资源组下创建流分析作业。 将在以后向作业添加输入、输出和转换。

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>创建流分析输入源
下面的代码将使用 blob 输入源类型和 CSV 序列化创建流分析输入源。 若要创建事件中心输入源，请使用 **EventHubStreamInputDataSource** 而非 **BlobStreamInputDataSource**。 同样，可以自定义输入源的序列化类型。

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

输入源（不管是来自 Blob 存储还是来自事件中心）将绑定到特定作业。 要将同一输入源用于不同的作业，必须再次调用该方法并指定不同的作业名称。

## <a name="test-a-stream-analytics-input-source"></a>测试流分析输入源
**TestConnection** 方法可测试流分析作业是否能够连接到输入源，并测试特定于输入源类型的其他方面。 例如，在 blob 输入源（已在此前的步骤中创建过）中，该方法会检查存储帐户名称和密钥对能否用于连接到存储帐户，并检查指定的容器是否存在。

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>创建流分析输出目标
创建输出目标非常类似于创建流分析输入源。 像输入源一样，输出目标会被绑定到特定的作业。 要将同一输出目标用于不同的作业，必须再次调用该方法并指定不同的作业名称。

下面的代码将创建一个输出目标（Azure SQL 数据库）。 可以自定义输出目标的数据类型和/或序列化类型。

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>测试流分析输出目标
流分析输出目标还有一个用于测试连接的 **TestConnection** 方法。

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>创建流分析转换
下面的代码将使用查询“select * from Input”创建流分析转换，并通过指定的方式为流分析作业分配一个流式处理单位。 有关如何调整流式处理单位的详细信息，请参阅[缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)。

    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

与输入和输出一样，转换也会绑定到在创建时所属的特定流分析作业。

## <a name="start-a-stream-analytics-job"></a>启动流分析作业
创建流分析作业及其输入、输出和转换后，可以通过调用 **Start** 方法来启动该作业。

下面的示例性代码将启动一个流分析作业，其自定义输出开始时间设置为 2012 年 12 月 12 日 12:12:12（UTC 时间）：

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>停止流分析作业
可以通过调用 **Stop** 方法来停止正在运行的流分析作业。

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>删除流分析作业
**Delete** 方法将删除作业以及基础性的子资源，包括作业的输入、输出和转换。

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
现已介绍完使用 .NET SDK 创建和运行分析作业的基础知识。 若要了解更多信息，请参阅下列文章：

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析管理 .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx)。
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
