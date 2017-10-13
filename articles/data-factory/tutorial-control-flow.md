---
title: "Azure 数据工厂管道中的分支 | Microsoft Docs"
description: "了解如何通过分支和链接活动控制 Azure 数据工厂中的数据流。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 2d1de12b420d203980d154e08dea036deb20a533
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>数据工厂管道中的分支和链接活动
Azure 数据工厂是基于云的数据集成服务，用于在云中创建数据驱动型工作流，以便协调和自动完成数据移动和数据转换。 使用 Azure 数据工厂，可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据，通过各种计算服务（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure 机器学习）处理/转换数据，将输出数据发布到数据存储（例如 Azure SQL 数据仓库），供商业智能 (BI) 应用程序使用。 

在本教程中，我们将创建一个数据工厂管道来展示某些控制流功能。 此管道执行从 Azure Blob 存储容器中某个容器到同一存储帐户中另一个容器的简单复制。 如果复制活动成功，可以在告知成功结果的电子邮件中发送成功复制操作的详细信息（例如写入的数据量）。 如果复制活动失败，可以在告知失败结果的电子邮件中发送复制失败的详细信息（例如错误消息）。 整个教程讲解了如何传递参数。

方案的综合概述：![概述](media/tutorial-control-flow/overview.png)

在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建 Azure 存储链接服务。
> * 创建 Azure Blob 数据集
> * 创建包含复制活动和 Web 活动的管道
> * 将活动的输出发送到后续活动
> * 利用参数传递和系统变量
> * 启动管道运行
> * 监视管道和活动运行

本教程使用 .NET SDK。 可以使用其他机制来与 Azure 数据工厂交互，具体请参阅目录中的“快速入门”。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

* **Azure 存储帐户**。 可将 Blob 存储用作**源**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。
* **Azure SQL 数据库**。 将数据库用作**接收器**数据存储。 如果没有 Azure SQL 数据库，请参阅[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)一文获取创建步骤。
* **Visual Studio** 2013、2015 或 2017。 本文中的演练使用 Visual Studio 2017。
* **下载并安装 [Azure .NET SDK](http://azure.microsoft.com/downloads/)**。
* 遵照[这些说明](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**在 Azure Active Directory 中创建应用程序**。 记下要在后续步骤中使用的以下值：**应用程序 ID**、**身份验证密钥**和**租户 ID**。 遵照同一文章中的以下说明将应用程序分配到“参与者”角色。

### <a name="create-blob-table"></a>创建 Blob 表

1. 启动记事本。 复制以下文本并在磁盘上将其另存为 **input.txt** 文件。

    ```
    John|Doe
    Jane|Doe
    ```
2. 使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具创建 **adfv2branch** 容器，并将 **input.txt** 文件上传到该容器。

## <a name="create-visual-studio-project"></a>创建 Visual Studio 项目

使用 Visual Studio 2015/2017 创建 C# .NET 控制台应用程序。

1. 启动 **Visual Studio**。
2. 单击“文件”，指向“新建”并单击“项目”。 需要 .NET 4.5.2 或更高版本。
3. 从右侧项目类型列表中选择“Visual C#” -> “控制台应用(.NET Framework)”。
4. 输入 **ADFv2BranchTutorial** 作为名称。
5. 单击“确定”以创建该项目  。

## <a name="install-nuget-packages"></a>安装 NuGet 包

1. 单击“工具” -> “NuGet 包管理器” -> “包管理器控制台”。
2. 在“包管理器控制台”中，运行以下命令来安装包：

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
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

2. Add these static variables to the **Program class**. Replace place-holders with your own values.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.
        // Currently, only East US and East US 2 regions are supported. 
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

3. 在 **Main** 方法中添加用于创建 **DataFactoryManagementClient** 类的实例的以下代码。 将使用此对象来创建数据工厂、链接服务、数据集和管道。 还将使用此对象来监视管道运行详细信息。

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>创建数据工厂
在 Program.cs 文件中创建“CreateOrUpdateDataFactory”函数：

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



在 **Main** 方法中添加用于创建**数据工厂**的以下代码。 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>创建 Azure 存储链接服务
在 Program.cs 文件中创建“StorageLinkedServiceDefinition”函数：

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
在 **Main** 方法中添加用于创建 **Azure 存储链接服务**的以下代码。 从 [Azure Blob 链接服务属性](connector-azure-blob-storage.md#linked-service-properties)了解更多有关受支持属性的信息及其细节。

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>创建数据集

在本部分中创建两个数据集：一个用于源，另一个用于接收器。 

### <a name="create-a-dataset-for-source-azure-blob"></a>为源 Azure Blob 创建数据集
向 **Main** 方法中添加用于创建 **Azure blob 数据集**的以下代码。 从 [Azure Blob 数据集属性](connector-azure-blob-storage.md#dataset-properties)了解更多有关受支持属性的信息及其细节。

在 Azure Blob 中定义表示源数据的数据集。 此 Blob 数据集引用在上一步中创建的 Azure 存储链接服务，并说明以下信息：

- 要从中复制的 Blob 的位置：**FolderPath** 和 **FileName**；
- 请注意 FolderPath 的参数用法。 “sourceBlobContainer”是参数的名称，表达式已替换为在管道运行中传递的值。 用于定义参数的语法为 `@pipeline().parameters.<parameterName>`

在 Program.cs 文件中创建“SourceBlobDatasetDefinition”函数

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

### <a name="create-a-dataset-for-sink-azure-blob"></a>为接收器 Azure Blob 创建数据集

在 Program.cs 文件中创建“SourceBlobDatasetDefinition”函数

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

在 **Main** 方法中添加用于创建 Azure Blob 源和接收器数据集的以下代码。 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>创建 C# 类：EmailRequest
在 C# 项目中，创建名为 **EmailRequest** 的类。 此类定义在发送电子邮件时，管道要在正文请求中发送哪些属性。 在本教程中，管道会将四个属性从管道发送到电子邮件：

- **消息**：电子邮件的正文。 如果复制成功，此属性包含运行的详细信息（写入的数据量）。 如果复制失败，此属性包含错误详细信息。
- **数据工厂名称**：数据工厂的名称
- **管道名称**：管道的名称
- **收件人**：传递的参数。 此属性指定电子邮件的收件人。

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
若要触发电子邮件的发送，可以使用[逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)来定义工作流。 有关创建逻辑应用工作流的详细信息，请参阅[如何创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 

### <a name="success-email-workflow"></a>成功电子邮件工作流 
创建名为 `CopySuccessEmail` 的逻辑应用工作流。 将工作流触发器定义为 `When an HTTP request is received`，并添加 `Office 365 Outlook – Send an email` 操作。

![成功电子邮件工作流](media/tutorial-control-flow/success-email-workflow.png)

对于请求触发器，请在 `Request Body JSON Schema` 中填写以下 JSON：

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
此值与前一部分中创建的 **EmailRequest** 类相符。 

逻辑应用设计器中的请求应如下所示：

![逻辑应用设计器 - 请求](media/tutorial-control-flow/logic-app-designer-request.png)

对于“发送电子邮件”操作，请使用传入请求正文 JSON 架构的属性来自定义如何设置电子邮件的格式。 下面是一个示例：

![逻辑应用设计器 - 发送电子邮件操作](media/tutorial-control-flow/send-email-action.png)

记下成功电子邮件工作流的 HTTP Post 请求 URL：

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>失败电子邮件工作流 
克隆 **CopySuccessEmail**，并创建另一个 **CopyFailEmail** 逻辑应用工作流。 在请求触发器中，`Request Body JSON schema` 是相同的。 只需更改电子邮件的格式（例如 `Subject`）即可定制失败电子邮件。 下面是一个示例：

![逻辑应用设计器 - 失败电子邮件工作流](media/tutorial-control-flow/fail-email-workflow.png)

记下失败电子邮件工作流的 HTTP Post 请求 URL：

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

现在，应有两个工作流 URL：

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>创建管道
在 Main 方法中添加用于创建包含复制活动和 dependsOn 属性的管道的以下代码。 在本教程中，该管道包含一个活动：这是一个复制活动，它使用 Blob 数据集作为源，使用另一个 Blob 数据集作为接收器。 复制活动成功和失败时，会调用不同的电子邮件任务。

在此管道中使用以下功能：

- parameters
- Web 活动
- 活动依赖项
- 使用一个活动的输出作为后续活动的输入

让我们将以下管道分解成不同的部分：

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
在 **Main** 方法中添加用于创建管道的以下代码：

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>parameters
管道的第一个部分定义参数。 

- sourceBlobContainer – 源 Blob 数据集使用的管道中的参数。
- sinkBlobContainer – 接收器 Blob 数据集使用的管道中的参数
- receiver – 两个 Web 活动使用的管道中的参数，电子邮件地址根据此参数接收电子邮件


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Web 活动
Web 活动允许调用任何 REST 终结点。 有关该活动的详细信息，请参阅 [Web 活动](control-flow-web-activity.md)。 此管道使用 Web 活动调用逻辑应用电子邮件工作流。 创建两个 Web 活动：一个调用 **CopySuccessEmail** 工作流，另一个调用 **CopyFailWorkFlow**。

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
在“Url”属性中，相应地粘贴来自逻辑应用工作流的请求 URL 终结点。 在“Body”属性中，传递“EmailRequest”类的实例。 电子邮件请求包含以下属性：

- 消息 – `@{activity('CopyBlobtoBlob').output.dataWritten` 的传递值。 访问前一复制活动的属性，并传递 dataWritten 的值。 失败时，传递错误输出而不是 `@{activity('CopyBlobtoBlob').error.message`。
- 数据工厂名称 – `@{pipeline().DataFactory}` 的传递值。这是一个系统变量，用于访问相应的数据工厂名称。 有关系统变量的列表，请参阅[系统变量](control-flow-system-variables.md)一文。
- 管道名称 – `@{pipeline().Pipeline}` 的传递值。 这也是系统变量，用于访问相应的管道名称。 
- 收件人 – "@pipeline().parameters.receiver") 的传递值。 访问管道参数。
 
此代码根据上一个成功的复制活动创建新的活动依赖项。

## <a name="create-a-pipeline-run"></a>创建管道运行
在 **Main** 方法中添加用于**触发管道运行**的以下代码。

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
最终的 Main 方法应如下所示。 生成并运行程序以触发管道运行！

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

## <a name="monitor-a-pipeline-run"></a>监视管道运行
1. 在 **Main** 方法中添加以下代码用于持续检查管道运行状态，直到它完成数据复制为止。

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

2. 在 **Main** 方法中添加以下代码用于检索复制活动运行详细信息，例如，读取/写入的数据大小。

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
控制台会输出数据工厂、链接服务、数据集、管道和管道运行的创建进度。 然后，检查管道运行状态。 请等到出现包含数据读取/写入大小的复制活动运行详细信息。 然后，使用 Azure 存储资源管理器等工具检查 Blob 是否已根据变量中的指定从“inputBlobPath”复制到“outputBlobPath”。

**示例输出：**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
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
已在本教程中执行了以下步骤： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建 Azure 存储链接服务。
> * 创建 Azure Blob 数据集
> * 创建包含复制活动和 Web 活动的管道
> * 将活动的输出发送到后续活动
> * 利用参数传递和系统变量
> * 启动管道运行
> * 监视管道和活动运行

现在可以转到“概念”部分详细了解 Azure 数据工厂。
> [!div class="nextstepaction"]
>[管道和活动](concepts-pipelines-activities.md)