---
title: Azure 数据工厂管道中的分支
description: 了解如何通过分支和链接活动控制 Azure 数据工厂中的数据流。
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 7ba921656d0dad059b1d15f443bcefeff03ade50
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977388"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>数据工厂管道中的分支和链接活动

在本教程中，我们将创建一个数据工厂管道来展示某些控制流功能。 此管道从 Azure Blob 存储容器中的某个容器复制到同一存储帐户中的另一个容器。 如果复制活动成功，该管道会在电子邮件中发送成功复制操作的详细信息。 该信息可能包括写入的数据量。 如果复制活动失败，该管道会在电子邮件中发送复制失败的详细信息（例如错误消息）。 整个教程讲解了如何传递参数。

下图概述了该方案：

![概述](media/tutorial-control-flow/overview.png)

本教程介绍如何执行以下任务：

> [!div class="checklist"]
> * 创建数据工厂
> * 创建 Azure 存储链接服务
> * 创建 Azure Blob 数据集
> * 创建包含复制活动和 Web 活动的管道
> * 将活动的输出发送到后续活动
> * 使用参数传递和系统变量
> * 启动管道运行
> * 监视管道和活动运行

本教程使用 .NET SDK。 可以使用其他机制来与 Azure 数据工厂交互。 有关数据工厂的快速入门，请参阅 [5 分钟快速入门](/azure/data-factory/quickstart-create-data-factory-portal)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

* Azure 存储帐户。 可将 Blob 存储用作源数据存储。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md)。
* Azure 存储资源管理器下载。 若要安装此工具，请参阅 [Azure 存储资源管理器](https://storageexplorer.com/)。
* Azure SQL 数据库。 将数据库用作接收器数据存储。 如果你没有 Azure SQL 数据库，请参阅[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)。
* Visual Studio。 本文使用 Visual Studio 2019。
* Azure .NET SDK。 下载并安装 [Azure .NET SDK](https://azure.microsoft.com/downloads/)。

有关当前可以使用数据工厂的 Azure 区域列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)。 数据存储和计算可以位于其他区域。 存储包括 Azure 存储和 Azure SQL 数据库。 计算包括数据工厂使用的 HDInsight。

根据[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)中所述创建一个应用程序。 按照同一文章中的以下说明将应用程序分配到“参与者”角色。  需要获取多个值（例如“应用程序(客户端) ID”和“目录(租户) ID”），以便在本教程的后续部分中使用。  

### <a name="create-a-blob-table"></a>创建 Blob 表

1. 打开文本编辑器。 复制以下文本，并在本地将其保存为 *input.txt*。

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. 打开 Azure 存储资源管理器。 展开你的存储帐户。 右键单击“Blob 容器”，并选择“创建 Blob 容器”。  
1. 将新容器命名为 *adfv2branch*，然后选择“上传”将 *input.txt* 文件添加到该容器。 

## 创建 Visual Studio 项目<a name="create-visual-studio-project"></a>

创建一个 C# .NET 控制台应用程序：

1. 启动 Visual Studio 并选择“创建新项目”。 
1. 在“创建新项目”中，选择适用于 C# 的“控制台应用(.NET Framework)”，然后选择“下一步”。   
1. 将项目命名为 *ADFv2BranchTutorial*。
1. 选择“.NET 版本 4.5.2”或更高版本，然后选择“创建”。  

### <a name="install-nuget-packages"></a>安装 NuGet 包

1. 选择“工具”   > “NuGet 包管理器”   > “包管理器控制台”  。
1. 在“包管理器控制台”  中，运行以下命令来安装包。 有关详细信息，请参阅 [Microsoft.Azure.Management.DataFactory NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)。

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>创建数据工厂客户端

1. 打开文件 *Program.cs* 并添加以下语句：

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

1. 将这些静态变量添加到 `Program` 类。 将占位符替换为自己的值。

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. 将以下代码添加到 `Main` 方法中。 此代码将创建 `DataFactoryManagementClient` 类的实例。 然后，你将使用此对象来创建数据工厂、链接服务、数据集和管道。 还可使用此对象来监视管道运行详细信息。

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>创建数据工厂

1. 将 `CreateOrUpdateDataFactory` 方法添加到 *Program.cs* 文件中：

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. 在 `Main` 方法中添加用于创建数据工厂的以下行：

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>创建 Azure 存储链接服务

1. 将 `StorageLinkedServiceDefinition` 方法添加到 *Program.cs* 文件中：

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. 在 `Main` 方法中添加用于创建 Azure 存储链接服务的以下行：

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

有关支持的属性和详细信息，请参阅[链接服务属性](connector-azure-blob-storage.md#linked-service-properties)。

## <a name="create-datasets"></a>创建数据集

在本部分中创建两个数据集：一个用于源，另一个用于接收器。

### <a name="create-a-dataset-for-a-source-azure-blob"></a>为源 Azure Blob 创建数据集

添加一个方法用于创建 *Azure Blob 数据集*。 有关支持的属性和详细信息，请参阅 [Azure Blob 数据集属性](connector-azure-blob-storage.md#dataset-properties)。

将 `SourceBlobDatasetDefinition` 方法添加到 *Program.cs* 文件中：

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

在 Azure Blob 中定义表示源数据的数据集。 此 Blob 数据集引用在上一步骤中支持的 Azure 存储链接服务。 Blob 数据集描述要从中复制 Blob 的位置：*FolderPath* 和 *FileName*。

请注意 *FolderPath* 的参数用法。 `sourceBlobContainer` 是参数的名称，表达式已替换为在管道运行中传递的值。 用于定义参数的语法为 `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>为接收器 Azure Blob 创建数据集

1. 将 `SourceBlobDatasetDefinition` 方法添加到 *Program.cs* 文件中：

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. 在 `Main` 方法中添加用于创建 Azure Blob 源和接收器数据集的以下代码。

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>创建 C# 类：EmailRequest

在 C# 项目中，创建名为 `EmailRequest` 的类。 此类定义在发送电子邮件时，管道要在正文请求中发送哪些属性。 在本教程中，管道会将四个属性从管道发送到电子邮件：

* 消息。 电子邮件的正文。 如果复制成功，此属性将包含写入的数据量。 如果复制失败，此属性将包含错误详细信息。
* 数据工厂名称。 数据工厂的名称。
* 管道名称。 管道的名称。
* 收件人。 传递的参数。 此属性指定电子邮件的收件人。

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>创建电子邮件工作流终结点

若要触发电子邮件的发送，可以使用[逻辑应用](../logic-apps/logic-apps-overview.md)来定义工作流。 有关创建逻辑应用工作流的详细信息，请参阅[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

### <a name="success-email-workflow"></a>成功电子邮件工作流

在 [Azure 门户](https://portal.azure.com)中，创建名为 *CopySuccessEmail* 的逻辑应用工作流。 将工作流触发器定义为 `When an HTTP request is received`。 对于请求触发器，请在 `Request Body JSON Schema` 中填写以下 JSON：

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

工作流类似于以下示例：

![成功电子邮件工作流](media/tutorial-control-flow/success-email-workflow-trigger.png)

此 JSON 内容与前一部分中创建的 `EmailRequest` 类相符。

添加 `Office 365 Outlook – Send an email` 操作。 对于“发送电子邮件”操作，请使用传入请求**正文** JSON 架构的属性来自定义如何设置电子邮件的格式。  下面是一个示例：

![逻辑应用设计器 - 发送电子邮件操作](media/tutorial-control-flow/customize-send-email-action.png)

保存工作流后，复制并保存触发器中的“HTTP POST URL”值。 

## <a name="fail-email-workflow"></a>失败电子邮件工作流

克隆 **CopySuccessEmail** 作为名为 *CopyFailEmail* 的另一个逻辑应用工作流。 在请求触发器中，`Request Body JSON schema` 是相同的。 更改电子邮件的格式（例如 `Subject`）即可定制失败电子邮件。 以下是示例：

![逻辑应用设计器 - 失败电子邮件工作流](media/tutorial-control-flow/fail-email-workflow.png)

保存工作流后，复制并保存触发器中的“HTTP POST URL”值。 

现在，应有两个工作流 URL，如以下示例所示：

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>创建管道

返回 Visual Studio 中的项目。 现在，我们添加代码来创建包含复制活动和 `DependsOn` 属性的管道。 在本教程中，该管道包含一个活动：这是一个复制活动，它使用 Blob 数据集作为源，使用另一个 Blob 数据集作为接收器。 复制活动成功或失败时，它会调用不同的电子邮件任务。

在此管道中使用以下功能：

* parameters
* Web 活动
* 活动依赖项
* 使用一个活动的输出作为另一活动的输入

1. 将此方法添加到项目。 以下部分提供了更多详细信息。

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. 在 `Main` 方法中添加用于创建管道的以下行：

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>parameters

管道代码的第一个部分定义参数。

* `sourceBlobContainer` 列中的一个值匹配。 源 Blob 数据集在管道中使用此参数。
* `sinkBlobContainer` 列中的一个值匹配。 接收器 Blob 数据集在管道中使用此参数。
* `receiver` 列中的一个值匹配。 管道中的两个 Web 活动使用此参数向收件人发送成功或失败电子邮件。

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Web 活动

Web 活动允许调用任何 REST 终结点。 有关该活动的详细信息，请参阅 [Azure 数据工厂中的 Web 活动](control-flow-web-activity.md)。 此管道使用 Web 活动调用逻辑应用电子邮件工作流。 创建两个 Web 活动：一个调用 `CopySuccessEmail` 工作流，另一个调用 `CopyFailWorkFlow`。

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

在 `Url` 属性中，粘贴来自逻辑应用工作流的“HTTP POST URL”终结点。  在 `Body` 属性中，传递 `EmailRequest` 类的实例。 电子邮件请求包含以下属性：

* 消息。 传递 `@{activity('CopyBlobtoBlob').output.dataWritten` 值。 访问前一复制活动的属性，并传递 `dataWritten` 值。 失败时，传递错误输出而不是 `@{activity('CopyBlobtoBlob').error.message`。
* 数据工厂名称。 传递 `@{pipeline().DataFactory}` 值。这是一个系统变量，用于访问相应的数据工厂名称。 有关系统变量的列表，请参阅[系统变量](control-flow-system-variables.md)。
* 管道名称。 传递 `@{pipeline().Pipeline}` 值。 此系统变量用于访问相应的管道名称。
* 收件人。 传递 `"@pipeline().parameters.receiver"` 值。 访问管道参数。

此代码创建依赖于上一个复制活动的新活动依赖项。

## <a name="create-a-pipeline-run"></a>创建管道运行

在 `Main` 方法中添加用于触发管道运行的以下代码。

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main 类

最终的 `Main` 方法应如下所示。

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

生成并运行程序以触发管道运行！

## <a name="monitor-a-pipeline-run"></a>监视管道运行

1. 将以下代码添加到 `Main` 方法中：

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    此代码持续检查运行状态，直到运行完成数据复制。

1. 在 `Main` 方法中添加以下代码用于检索复制活动运行详细信息，例如，读取/写入的数据大小。

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>运行代码

生成并启动应用程序，然后验证管道执行。

应用程序将显示数据工厂、链接服务、数据集、管道和管道运行的创建进度。 然后，检查管道运行状态。 请等到出现包含数据读取/写入大小的复制活动运行详细信息。 然后，使用 Azure 存储资源管理器等工具检查 Blob 是否已根据变量中指定的 *inputBlobPath* 复制到 *outputBlobPath*。

输出应类似于以下示例：

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已执行以下任务：

> [!div class="checklist"]
> * 创建数据工厂
> * 创建 Azure 存储链接服务
> * 创建 Azure Blob 数据集
> * 创建包含复制活动和 Web 活动的管道
> * 将活动的输出发送到后续活动
> * 使用参数传递和系统变量
> * 启动管道运行
> * 监视管道和活动运行

现在可以转到“概念”部分来详细了解 Azure 数据工厂。
> [!div class="nextstepaction"]
>[管道和活动](concepts-pipelines-activities.md)
