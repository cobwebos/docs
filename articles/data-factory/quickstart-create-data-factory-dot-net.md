---
title: 使用 .NET SDK 创建 Azure 数据工厂
description: 创建一个 Azure 数据工厂，将数据从 Azure Blob 存储中的一个位置复制到另一位置。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2019
ms.author: jingwang
ms.openlocfilehash: 3282106651f9ec101251d7d35369040df9572b06
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122860"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>快速入门：使用 .NET SDK 创建数据工厂和管道

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [当前版本](quickstart-create-data-factory-dot-net.md)

此快速入门介绍了如何使用 .NET SDK 创建一个 Azure 数据工厂。 在此数据工厂中创建的管道会将数据从 Azure Blob 存储中的一个文件夹**复制**到另一个文件夹。 有关如何使用 Azure 数据工厂**转换**数据的教程，请参阅[教程：使用 Spark 转换数据](tutorial-transform-data-spark-portal.md)。

> [!NOTE]
> 本文不提供数据工厂服务的详细介绍。 有关 Azure 数据工厂服务的介绍，请参阅 [Azure 数据工厂简介](introduction.md)。

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

本文中的演练使用 Visual Studio 2019。 Visual Studio 2013、2015 或 2017 的过程略有不同。

### <a name="azure-net-sdk"></a>Azure .NET SDK

在计算机上下载并安装 [Azure .NET SDK](https://azure.microsoft.com/downloads/)。

## <a name="create-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中创建应用程序

从“如何：  使用门户创建可访问资源的 Azure AD 应用程序和服务主体”中的各部分开始，按照说明执行以下任务：

1. 在[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)中，创建一个应用程序来表示正在本教程中创建的 .NET 应用程序。 对于登录 URL，可以提供虚拟 URL，如本文中所示 (`https://contoso.org/exampleapp`)。
2. 在[获取用于登录的值](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)中，获取应用程序 ID 和租户 ID，并记下这些值，稍后要在本教程中使用它们   。 
3. 在[证书与机密](../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets)中，获取身份验证密钥，并记此值，稍后要在本教程中使用它  。
4. 在[将应用程序分配给角色](../active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role)中，在订阅级别将应用程序分配到“参与者”角色，让该应用程序可以在订阅中创建数据工厂  。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

接下来，在 Visual Studio 中创建 C# 控制台应用程序：

1. 启动 **Visual Studio**。
2. 在“开始”窗口中，选择“创建新项目” > “控制台应用(.NET Framework)”   。 需要 .NET 4.5.2 或更高版本。
3. 在“项目名称”中，输入 ADFv2QuickStart   。
4. 选择“创建”  来创建项目。

## <a name="install-nuget-packages"></a>安装 NuGet 包

1. 选择“工具”   > “NuGet 包管理器”   > “包管理器控制台”  。
2. 在“包管理器控制台”窗格中，运行以下命令来安装包  。 有关详细信息，请参阅 [Microsoft.Azure.Management.DataFactory NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)。

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>创建数据工厂客户端

1. 打开 **Program.cs**，包括以下语句来添加对命名空间的引用。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 将以下代码添加到 **Main** 方法以设置变量。 将占位符替换为自己的值。 若要查看目前提供数据工厂的 Azure 区域的列表，请在以下页面上选择感兴趣的区域，然后展开“分析”  以找到“数据工厂”  ：[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/)。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库等）和计算资源（HDInsight 等）可以位于其他区域中。

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. 在 **Main** 方法中添加用于创建 **DataFactoryManagementClient** 类的实例的以下代码。 将使用此对象创建数据工厂、链接服务、数据集以及管道。 还将使用此对象来监视管道运行详细信息。

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>创建数据工厂

向 **Main** 方法中添加用于创建**数据工厂**的以下代码。 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>创建链接服务

在 **Main** 方法中添加用于创建 **Azure 存储链接服务**的以下代码。

可在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在此快速入门中，只需创建一个 Azure 存储链接服务，用于复制源和接收器存储。在示例中，此服务名为“AzureStorageLinkedService”。

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>创建数据集

向 **Main** 方法中添加用于创建 **Azure blob 数据集**的以下代码。

定义一个数据集来表示要从源复制到接收器的数据。 在此示例中，此 Blob 数据集引用在上一步中创建的 Azure 存储链接服务。 此数据集采用一个参数，其值在使用此数据集的活动中设置。 该参数用来构造“folderPath”，该项指向数据的驻留/存储位置。

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>创建管道

向 **Main** 方法中添加用于创建**包含复制活动的管道**的以下代码。

在此示例中，此管道包含一个活动并采用两个参数：输入 Blob 路径和输出 Blob 路径。 这些参数的值是在触发/运行管道时设置的。 复制活动引用在上一步中创建的同一 blob 数据集作为输入和输出。 当该数据集用作输入数据集时，即指定了输入路径。 并且，当该数据集用作输出数据集时，即指定了输出路径。 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>创建管道运行

在 **Main** 方法中添加用于**触发管道运行**的以下代码。

此代码还设置 inputPath 和 outputPath 参数的值，这些值是使用源和接收器 Blob 路径的实际值在管道中指定的值   。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>监视管道运行

1. 在 **Main** 方法中添加以下代码用于持续检查状态，直到它完成数据复制为止。

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. 在 Main 方法中添加以下代码用于检索复制活动运行详细信息，例如，读取/写入的数据大小  。

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>运行代码

生成并启动应用程序，然后验证管道执行。

控制台会输出数据工厂、链接服务、数据集、管道和管道运行的创建进度。 然后，检查管道运行状态。 请等待，直至看到包含数据读取/写入大小的复制活动运行详细信息。 然后，使用 [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等工具检查 Blob 是否已根据变量中的指定从“inputBlobPath”复制到“outputBlobPath”。

### <a name="sample-output"></a>示例输出

```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>验证输出

该管道自动在 adftutorial Blob 容器中创建 output 文件夹  。 然后，将 emp.txt 文件从 input 文件夹复制到 output 文件夹  。 

1. 在 Azure 门户中，在你在以上[为 Blob 容器添加输入文件夹和文件](#add-an-input-folder-and-file-for-the-blob-container)部分中停留的 adftutorial 容器页上，选择“刷新”以查看输出文件夹   。 
2. 在文件夹列表中，选择“output”  。
3. 确认 **emp.txt** 已复制到 output 文件夹。 

## <a name="clean-up-resources"></a>清理资源

若要以编程方式删除数据工厂，请向程序中添加以下代码行： 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>后续步骤

此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 完成相关[教程](tutorial-copy-data-dot-net.md)来了解如何在更多方案中使用数据工厂。 
