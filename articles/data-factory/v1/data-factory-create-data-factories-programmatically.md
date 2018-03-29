---
title: 使用 Azure .NET SDK 创建数据管道 | Microsoft Docs
description: 了解如何使用数据工厂 SDK 以编程方式创建、监视和管理 Azure 数据工厂。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 73cdb2fc2ed57a55dbac7d0c8f66c96f501a3e04
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>使用 Azure 数据工厂 .NET SDK 创建、监视和管理 Azure 数据工厂
> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[版本 2 中的复制活动教程文档](../quickstart-create-data-factory-dot-net.md)。 

## <a name="overview"></a>概述
可使用数据工厂 .NET SDK 以编程方式创建、监视和管理 Azure 数据工厂。 可根据本文所含演练创建示例 .NET 控制台应用程序，从而创建和监视数据工厂。 

> [!NOTE]
> 本文不会介绍所有数据工厂 .NET API。 有关数据工厂 .NET API 的综合文档，请参阅 [Data Factory .NET API Reference](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1)（数据工厂 .NET API 参考）。 

## <a name="prerequisites"></a>先决条件
* Visual Studio 2012、2013 或 2015
* 下载并安装 [Azure .NET SDK](http://azure.microsoft.com/downloads/)。
* Azure PowerShell。 遵循 [How to install and configure Azure PowerShell](/powershell/azure/overview) （如何安装和配置 Azure PowerShell）一文中的说明，在计算机上安装 Azure PowerShell。 使用 Azure PowerShell 创建 Azure Active Directory 应用程序。

### <a name="create-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中创建应用程序
创建一个 Azure Active Directory 应用程序，为该应用程序创建服务主体，然后将其分配到 **数据工厂参与者** 角色。

1. 启动 **PowerShell**。
2. 运行以下命令并输入用于登录 Azure 门户的用户名和密码。

    ```PowerShell
    Login-AzureRmAccount
    ```
3. 运行以下命令查看此帐户的所有订阅。

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. 运行以下命令选择要使用的订阅。 将 **&lt;NameOfAzureSubscription**&gt; 替换为 Azure 订阅的名称。

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > 记下此命令的输出中的 **SubscriptionId** 和 **TenantId**。

5. 在 PowerShell 中运行以下命令，创建名为 **ADFTutorialResourceGroup** 的 Azure 资源组。

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    如果资源组已存在，请指定是要更新它 (Y) 还是保留原样 (N)。

    如果使用不同的资源组，需使用该资源组的名称取代本教程中的 ADFTutorialResourceGroup。
6. 创建 Azure Active Directory 应用程序。

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    如果看到以下错误，请指定不同的 URL，并再次运行该命令。
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. 创建 AD 服务主体。

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. 将服务主体添加到 **数据工厂参与者** 角色。

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. 获取应用程序 ID。

    ```PowerShell
    $azureAdApplication 
    ```
    记下输出中的应用程序 ID (applicationID)。

应该通过相应的步骤获取以下四个值：

* 租户 ID
* 订阅 ID
* 应用程序 ID
* 密码（在第一条命令中指定）

## <a name="walkthrough"></a>演练
在本演练中，将使用包含复制活动的管道创建数据工厂。 复制活动将数据从 Azure blob 存储中的一个文件夹复制到同一 blob 存储中的另一个文件夹。 

复制活动在 Azure 数据工厂中执行数据移动。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅 [Data Movement Activities](data-factory-data-movement-activities.md) （数据移动活动）。

1. 使用 Visual Studio 2012/2013/2015 创建 C# .NET 控制台应用程序。
   1. 启动 **Visual Studio** 2012/2013/2015。
   2. 单击“文件”，指向“新建”并单击“项目”。
   3. 展开“模板”，并选择“Visual C#”。 本演练中使用的是 C#，但可以使用任何 .NET 语言。
   4. 从右侧项目类型列表中选择“控制台应用程序”。
   5. 在“名称”中输入 **DataFactoryAPITestApp** 。
   6. 在“位置”中选择“C:\ADFGetStarted”。
   7. 单击“确定”以创建该项目  。
2. 单击“工具”，指向“NuGet 包管理器”，并单击“包管理器控制台”。
3. 在“包管理器控制台”中执行以下步骤：
   1. 运行以下命令安装数据工厂包：`Install-Package Microsoft.Azure.Management.DataFactories`
   2. 运行以下命令安装 Azure Active Directory 包（因为要在代码中使用 Active Directory API）：`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. 将项目中 **App.config** 文件的内容替换为以下内容： 
    
    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```
5. 在 App.Config 文件中，使用自己的值更新 **&lt;Application ID&gt;**、**&lt;Password&gt;**、**&lt;Subscription ID&gt;** 和 **&lt;tenant ID&gt;** 的值。
6. 将以下 **using** 语句添加到项目中的 **Program.cs** 文件。

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. 将以下用于创建 **DataPipelineManagementClient** 类实例的代码添加到 **Main** 方法。 将使用此对象创建数据工厂、链接服务、输入和输出数据集以及管道。 此外，在运行时会使用此对象监视数据集切片。

    ```csharp
    // create data factory management client

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > 将 **resourceGroupName** 的值替换为 Azure 资源组的名称。 可使用 [New-AzureResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet 创建资源组。
   >
   > 将数据工厂的名称 (dataFactoryName) 更新为唯一名称。 数据工厂的名称必须全局唯一。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。
7. 将以下用于创建**数据工厂**的代码添加到 **Main** 方法。

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. 将以下用于创建 **Azure 存储链接服务**的代码添加到 **Main** 方法。

   > [!IMPORTANT]
   > 将 **storageaccountname** 和 **accountkey** 分别替换为 Azure 存储帐户的名称和密钥。

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```
9. 将以下用于创建**输入和输出数据集**的代码添加到 **Main** 方法。

    输入 blob 的 **FolderPath** 设置为 **adftutorial/**，其中 **adftutorial** 是 Blob 存储中的容器名称。 如果 Azure Blob 存储中不包含此容器，请创建名为 **adftutorial** 的容器，并将文本文件上传到该容器。

    输出 blob 的 FolderPath 设置为 **adftutorial/apifactoryoutput/{Slice}**，在此位置基于 **SliceStart** 的值（每个切片的开始日期时间）动态评估**切片**。

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. 将以下用于**创建和激活管道**的代码添加到 **Main** 方法。 此管道包含一个源为 **BlobSource**、接收器为 **BlobSink** 的 **CopyActivity**。

    复制活动在 Azure 数据工厂中执行数据移动。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅 [Data Movement Activities](data-factory-data-movement-activities.md) （数据移动活动）。

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
12. 将以下代码添加 **Main** 方法，获取输出数据集的数据切片状态。 本示例应只有一个切片。

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **（可选）**将以下用于获取数据切片运行详细信息的代码添加到 **Main** 方法。

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
        resourceGroupName,
        dataFactoryName,
        Dataset_Destination,
        new DataSliceRunListParameters()
        {
            DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
        });
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. 将 **Main** 方法使用的以下帮助器方法添加到 **Program** 类。 此方法会弹出要求提供用于登录 Azure 门户的**用户名**和**密码**的对话框。

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. 在“解决方案资源管理器”中展开项目 **DataFactoryAPITestApp**，右键单击“引用”，并单击“添加引用”。 选择 `System.Configuration` 程序集的复选框，并单击“确定”。
15. 生成控制台应用程序。 在菜单中单击“生成”，并单击“生成解决方案”。
16. 确认 Azure Blob 存储中的 adftutorial 容器内至少有一个文件。 如果没有，请在记事本中创建包含以下内容的 Emp.txt 文件，并将其上传到 adftutorial 容器。

    ```
    John, Doe
    Jane, Doe
    ```
17. 在菜单中单击“调试” -> “开始调试”运行示例。 看到“正在获取数据切片的运行详细信息”时，请等待几分钟，并按 **ENTER**。
18. 使用 Azure 门户验证是否创建了包含以下项目的数据工厂 **APITutorialFactory** ：
    * 链接服务：**AzureStorageLinkedService**
    * 数据集：**DatasetBlobSource** 和 **DatasetBlobDestination**。
    * 管道： **PipelineBlobSample**
19. 验证 **adftutorial** 容器中的 **apifactoryoutput** 文件夹内是否创建了一个输出文件。

## <a name="get-a-list-of-failed-data-slices"></a>获取失败的数据切片的列表 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>后续步骤
请参阅以下示例，该示例使用 .NET SDK 创建将数据从 Azure blob 存储复制到 Azure SQL 数据库的管道： 

- [创建用于将数据从 Blob 存储复制到 SQL 数据库的管道](data-factory-copy-activity-tutorial-using-dotnet-api.md)
