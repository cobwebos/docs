---
title: "使用数据工厂 SDK 创建、监视和管理 Azure 数据工厂 | Microsoft Docs"
description: "了解如何使用数据工厂 SDK 以编程方式创建、监视和管理 Azure 数据工厂。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: ebc5dbf790ca6012cfe9a7ea9ccee9fdacb46ffd
ms.openlocfilehash: d51c900f7cf69d3852a1699284a459b930eb735e


---
# <a name="create-monitor-and-manage-azure-data-factories-using-data-factory-net-sdk"></a>使用数据工厂 .NET SDK 创建、监视和管理 Azure 数据工厂
## <a name="overview"></a>概述
可使用数据工厂 .NET SDK 以编程方式创建、监视和管理 Azure 数据工厂。 可根据本文所含演练创建示例 .NET 控制台应用程序，从而创建和监视数据工厂。 有关数据工厂 .NET SDK 的详细信息，请参阅[数据工厂类库参考](https://msdn.microsoft.com/library/mt415893.aspx)。

## <a name="prerequisites"></a>先决条件
* Visual Studio 2012、2013 或 2015
* 下载并安装 [Azure .NET SDK](http://azure.microsoft.com/downloads/)。
* 将本机客户端应用程序添加到 Azure Active Directory。 有关添加应用程序的步骤，请参阅[将应用程序与 Azure Active Directory 集成](../active-directory/active-directory-integrating-applications.md)。 记下“配置”页上的**客户端 ID** 和**重定向 URI**。
* 获取 Azure **订阅 ID**和**租户 ID**。 有关说明，请参阅[获取 Azure 订阅 ID和租户 ID](#get-azure-subscription-and-tenant-ids)。
* 下载并安装用于 Azure 数据工厂的 NuGet 包。 本演练提供说明。

## <a name="walkthrough"></a>演练
1. 使用 Visual Studio 2012 或 2013 创建 C# .NET 控制台应用程序。
   1. 启动 **Visual Studio 2012/2013/2015**。
   2. 单击“文件”，指向“新建”并单击“项目”。
   3. 展开“模板”，然后选择“Visual C#”。 本演练中使用的是 C#，但可以使用任何 .NET 语言。
   4. 从右侧项目类型列表中选择“控制台应用程序”。
   5. 在“名称”中输入 **DataFactoryAPITestApp**。
   6. 对于“位置”，选择“C:\ADFGetStarted”。
   7.  。
2. 单击“工具”，指向“NuGet 包管理器”，然后单击“包管理器控制台”。
3. 在“包管理器控制台”中，逐条执行以下命令。

    ```
    Install-Package Microsoft.Azure.Management.DataFactories
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    ```
4. 将以下 **appSettings** 节添加到 **App.config** 文件。 这些配置值由 **GetAuthorizationHeader** 方法使用。

   > [!IMPORTANT]
   > 将 **AdfClientId**、**RedirectUri**、**SubscriptionId** 和 **ActiveDirectoryTenantId** 的值替换为自己的值。

    ```XML
    <appSettings>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    
        <!-- Replace the following values with your own -->
        <add key="AdfClientId" value="Your AAD application ID" />
        <add key="RedirectUri" value="Your AAD application's redirect URI" />
        <add key="SubscriptionId" value="your subscription ID" />
        <add key="ActiveDirectoryTenantId" value="your tenant ID" />
    </appSettings>
    ```
5. 将以下 **using** 语句添加到项目中的源文件 (Program.cs)。

    ```csharp
    using System.Threading;
    using System.Configuration;
    using System.Collections.ObjectModel;

    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure;
    ```
6. 将以下用于创建 **DataPipelineManagementClient** 类实例的代码添加到 **Main** 方法。 将使用此对象创建数据工厂、链接服务、输入和输出数据集以及管道。 此外，在运行时将使用此对象监视数据集切片。

    ```csharp
    // create data factory management client
    string resourceGroupName = "resourcegroupname";
    string dataFactoryName = "APITutorialFactorySP";
    
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());
    
    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
    
    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!NOTE]
   > 将 **resourcegroupname** 替换为 Azure 资源组的名称。 可使用 [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) cmdlet 创建资源组。
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
                Properties = new DataFactoryProperties() { }
            }
        }
    );
    ```
8. 将以下用于创建**链接服务**的代码添加到 **Main** 方法。

   > [!NOTE]
   > 对 **ConnectionString** 使用 Azure 存储帐户的**帐户名**和**帐户密钥**。

    ```csharp
    // create a linked service
    Console.WriteLine("Creating a linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "LinkedService-AzureStorage",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                )
            }
        }
    );
    ```
9. 将以下用于创建**输入和输出数据集**的代码添加到 **Main** 方法。

    输入 blob 的 **FolderPath** 设置为 **adftutorial/**，其中 **adftutorial** 是 Blob 存储中的容器名称。 如果 Azure Blob 存储中不包含此容器，请创建名为 **adftutorial** 的容器，然后将文本文件上传到该容器。

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
                LinkedServiceName = "LinkedService-AzureStorage",
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
    
                LinkedServiceName = "LinkedService-AzureStorage",
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
                            new ActivityInput() {
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
11. 将 **Main** 方法使用的以下帮助器方法添加到 **Program** 类。 此方法会弹出要求提供用于登录 Azure 门户的**用户名**和**密码**的对话框。

    ```csharp
    public static string GetAuthorizationHeader()
    {
        AuthenticationResult result = null;
        var thread = new Thread(() =>
        {
            try
            {
                var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                result = context.AcquireToken(
                    resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                    clientId: ConfigurationManager.AppSettings["AdfClientId"],
                    redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                    promptBehavior: PromptBehavior.Always);
            }
            catch (Exception threadEx)
            {
                Console.WriteLine(threadEx.Message);
            }
        });

        thread.SetApartmentState(ApartmentState.STA);
        thread.Name = "AcquireTokenThread";
        thread.Start();
        thread.Join();

        if (result != null)
        {
            return result.AccessToken;
        }

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```
12. 将以下代码添加 **Main** 方法，获取输出数据集的数据切片状态。 本示例预期只有切片。

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
            }
        );
    
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
14. 在解决方案资源管理器中展开项目 (**DataFactoryAPITestApp**)，右键单击“引用”，然后单击“添加引用”。 选择 `System.Configuration` 程序集的复选框，然后单击“确定”。
15. 生成控制台应用程序。 在菜单中单击“生成”，然后单击“生成解决方案”。
16. 确认 Azure Blob 存储中的 adftutorial 容器内至少有一个文件。 如果没有，请在记事本中创建包含以下内容的 Emp.txt 文件，然后将其上传到 adftutorial 容器。

    ```
    John, Doe
    Jane, Doe
    ```
17. 在菜单中单击“调试” -> “开始调试”运行示例。 看到“正在获取数据切片的运行详细信息”时，请等待几分钟，然后按 **ENTER**。
18. 使用 Azure 门户验证是否创建了包含以下项目的数据工厂 **APITutorialFactory** ：
    * 链接服务：**LinkedService_AzureStorage**
    * 数据集：**DatasetBlobSource** 和 **DatasetBlobDestination**。
    * 管道： **PipelineBlobSample**
19. 验证 **adftutorial** 容器中的 **apifactoryoutput** 文件夹内是否创建了一个输出文件。

## <a name="log-in-without-popup-dialog-box"></a>不使用弹出对话框登录
本演练中的示例代码可启动用于输入 Azure 凭据的对话框。 如果需要不使用对话框以编程方式登录，请参阅[使用 Azure Resource Manager 对服务主体进行身份验证](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

> [!IMPORTANT]
> 将 Web 应用程序添加到 Azure Active Directory，并记下该应用程序的客户端 ID 和客户端密码。
>
>

### <a name="example"></a>示例
创建 GetAuthorizationHeaderNoPopup 方法。

```csharp
public static string GetAuthorizationHeaderNoPopup()
{
    var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
    var context = new AuthenticationContext(authority.AbsoluteUri);
    var credential = new ClientCredential(ConfigurationManager.AppSettings["AdfClientId"], ConfigurationManager.AppSettings["AdfClientSecret"]);
    AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings["WindowsManagementUri"], credential).Result;
    if (result != null)
        return result.AccessToken;

    throw new InvalidOperationException("Failed to acquire token");
}
```

将 **GetAuthorizationHeader** 调用替换为对 **Main** 函数中 **GetAuthorizationHeaderNoPopup** 的调用：

```csharp
TokenCloudCredentials aadTokenCredentials =
    new TokenCloudCredentials(
    ConfigurationManager.AppSettings["SubscriptionId"],
    GetAuthorizationHeaderNoPopup());
```

下面介绍如何创建 Active Directory 应用程序、服务主体，然后将其分配给“数据工厂参与者”角色：

1. 创建 AD 应用程序。

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"
    ```
2. 创建 AD 服务主体。

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
3. 将服务主体添加到“数据工厂参与者”角色。

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
4. 获取应用程序 ID。

    ```PowerShell
    $azureAdApplication
    ```

记下应用程序 ID 和密码（客户端密码），并在本演练中使用它们。

## <a name="get-azure-subscription-and-tenant-ids"></a>获取 Azure 订阅和租户 ID
如果尚未在计算机上安装最新版本的 PowerShell，请遵循[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)一文中的说明对其进行安装。

1. 启动 Azure PowerShell 并运行以下命令
2. 运行以下命令并输入用于登录 Azure 门户的用户名和密码。

    ```PowerShell
    Login-AzureRmAccount
    ```

    如果仅有一个与此帐户关联的 Azure 订阅，则不必执行以下两个步骤。
3. 运行以下命令查看此帐户的所有订阅。

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. 运行以下命令选择要使用的订阅。 将 **NameOfAzureSubscription** 替换为 Azure 订阅的名称。

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext
    ```PowerShell

Note down the **SubscriptionId** and **TenantId** values.



<!--HONumber=Dec16_HO4-->


