---
title: "使用 Azure Batch 服务 API 将作业和任务输出保存到 Azure 存储 | Microsoft Docs"
description: "了解如何使用 Batch 服务 API 将 Batch 任务和作业输出保存到 Azure 存储。"
services: batch
author: tamram
manager: timlt
editor: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: b3a4e8f9c8580ad4c7899964dbfe99ad74e0c744
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017


---


# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>使用 Batch 服务 API 将任务数据保存到 Azure 存储

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

从版本 2017-05-01 开始，Batch 服务 API 支持将采用虚拟机配置的池上运行的任务和作业管理器任务的输出数据保存到 Azure 存储。 添加任务时，可将 Azure 存储中的某个容器指定为该任务的输出目标。 然后，在完成该任务时，Batch 服务会将所有输出数据写入该容器。

使用 Batch 服务 API 保存任务输出的优势之一是不需要修改任务运行的应用程序。 只需对客户端应用程序进行几处简单的修改，即可从创建任务的代码内部保存任务的输出。   

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>何时使用 Batch 服务 API 保存任务输出？

Azure Batch 提供多种方式来保存任务输出。 使用 Batch 服务 API 是一种便捷的方法，最适合以下情况：

- 希望编写代码来从客户端应用程序内部保存任务输出，而无需修改任务运行的应用程序。
- 希望保存使用虚拟机配置创建的池中的 Batch 任务和作业管理器任务的输出。
- 希望将输出保存到具有任意名称的 Azure 存储容器。
- 希望将输出保存到按照 [Batch 文件约定标准](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)命名的 Azure 存储容器。 

如果你的情况与上面不同，可能需要考虑不同的方法。 例如，在运行任务时，Batch 服务 API 目前不支持将输出流式传输到 Azure 存储。 若要流式传输输出，请考虑使用适用于 .NET 的 Batch 文件约定库。 对于其他语言，需要实现自己的解决方案。 有关保存任务输出的其他选项的详细信息，请参阅[将作业和任务输出保存到 Azure 存储](batch-task-output.md)。 

## <a name="create-a-container-in-azure-storage"></a>在 Azure 存储中创建容器

若要将任务输出保存到 Azure 存储，需要创建一个充当输出文件目标的容器。 请在运行任务之前创建容器，最好是在提交作业之前创建。 若要创建容器，请使用适当的 Azure 存储客户端库或 SDK。 有关 Azure 存储 API 的详细信息，请参阅 [Azure 存储文档](https://docs.microsoft.com/azure/storage/)。

例如，在以 C# 编写应用程序时，可以使用[适用于 .NET 的 Azure 存储客户端库](https://www.nuget.org/packages/WindowsAzure.Storage/)。 以下示例演示如何创建容器：

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>获取容器的共享访问签名

创建容器后，请获取对容器拥有写入访问权限的共享访问签名 (SAS)。 SAS 提供对容器的委派访问权限。 SAS 使用一组指定的权限授予在指定时间间隔内的访问权限。 Batch 服务需要一个拥有写入权限的 SAS 才能将任务输出写入到容器。 有关 SAS 的详细信息，请参阅[在 Azure 存储中使用共享访问签名 \(SAS\)](../storage/storage-dotnet-shared-access-signature-part-1.md)。

使用 Azure 存储 API 获取 SAS 时，API 将返回 SAS 令牌字符串。 此令牌字符串包含 SAS 的所有参数，其中包括权限以及 SAS 的生效间隔。 若要使用 SAS 访问 Azure 存储中的容器，需要将 SAS 令牌字符串追加到资源 URI。 资源 URI 连同追加的 SAS 令牌中提供对 Azure 存储的经过身份验证的访问权限。

以下示例演示如何获取容器的只写 SAS 令牌字符串，然后将 SAS 追加到容器 URI：

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## <a name="specify-output-files-for-task-output"></a>指定任务输出的输出文件

若要指定任务的输出文件，请创建 [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) 对象的集合，并在创建任务时将该集合分配到 [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) 属性。 

以下 .NET 代码示例创建一个可将随机数写入名为 `output.txt` 的文件的任务。 该示例为要写入容器的 `output.txt` 创建输出文件。 该示例还为匹配文件模式 `std*.txt`（例如 `stdout.txt` 和 `stderr.txt`）的所有日志文件创建输出文件。 容器 URL 需要先前为容器创建的 SAS。 Batch 服务使用 SAS 对容器的访问进行身份验证： 

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>指定用于匹配的文件模式

指定输出文件时，可以使用 [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) 属性指定用于匹配的文件模式。 文件模式可以匹配零个文件、一个文件或者任务创建的一组文件。

**FilePattern** 属性支持标准文件系统通配符，例如 `*`（表示非递归匹配）和 `**`（表示递归匹配）。 例如，上述代码示例指定要以非递归方式匹配 `std*.txt` 的文件模式： 

`filePattern: @"..\std*.txt"`

若要上传单个文件，请指定不带通配符的文件模式。 例如，上述代码示例指定用于匹配 `output.txt` 的文件模式：

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>指定上传条件

[OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) 属性允许按条件上传输出文件。 一种常见方案是任务成功时上传一组文件，任务失败时上传另一组文件。 例如，你可能希望仅当任务失败并使用非零退出代码退出时，才上传详细日志文件。 同样，你可能希望仅当任务成功时才上传结果文件，因为任务失败时这些文件可能会丢失或不完整。

上述代码示例将 **UploadCondition** 属性设置为 **TaskCompletion**。 此设置指定在任务完成后上传文件，不管退出代码的值是什么。 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

有关其他设置，请参阅 [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) 枚举。

### <a name="disambiguate-files-with-the-same-name"></a>区分同名的文件

作业中的任务可能会生成同名的文件。 例如，对于作业中运行的每个任务，都会创建 `stdout.txt` 和 `stderr.txt`。 由于每个任务在其自身的上下文中运行，因此这些文件在节点的文件系统中不会冲突。 但是，在将多个任务生成的文件上传到共享容器时，需要区分同名的文件。

[OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) 属性指定输出文件的目标 Blob 或虚拟目录。 可以使用 **Path** 属性来命名 Blob 或虚拟目录，使得同名的输出文件在 Azure 存储中具有唯一的名称。 在路径中使用任务 ID 是一种很好的做法，可以确保名称唯一，同时轻松识别文件。

如果 **FilePattern** 属性设置为通配符表达式，则与模式匹配的所有文件将上传到 **Path** 属性指定的虚拟目录。 例如，如果容器为 `mycontainer`，任务 ID 为 `mytask`，文件模式为 `..\std*.txt`，则输出文件在 Azure 存储中的绝对 URI 将类似于：

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

如果 **FilePattern** 属性设置为匹配单个文件名（意味着不包含任何通配符），则 **Path** 属性的值将指定完全限定的 Blob 名称。 如果预期命名会与多个任务生成的单个文件相冲突，在文件名中包含虚拟目录的名称即可区分这些文件。 例如，将 **Path** 属性设置为包含任务 ID、分隔符（通常是正斜杠）和文件名：

`path: taskId + @"/output.txt"`

一组任务的输出文件的绝对 URI 类似于：

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

有关 Azure 存储中的虚拟目录的详细信息，请参阅[列出容器中的 Blob](../storage/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container)。


## <a name="diagnose-file-upload-errors"></a>诊断文件上传错误

如果将输出文件上传到 Azure 存储失败，该任务将转为 **Completed** 状态，同时系统会设置 [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) 属性。 检查 **FailureInformation** 属性可确定发生了哪种错误。 例如，下面是在找不到容器的情况下，上传文件时发生的错误： 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

每次上传文件时，Batch 都会将以下两个日志文件写入计算节点：`fileuploadout.txt` 和 `fileuploaderr.txt`。 可以检查这些日志文件来详细了解具体的失败情况。 如果从未尝试上传文件（例如，因为任务本身无法运行），则这些日志文件不会存在。

## <a name="diagnose-file-upload-performance"></a>诊断文件上传的性能

`fileuploadout.txt` 文件记录了上传进度。 可以检查此文件来详细了解上传文件所花费的时间。 请记住，有许多因素会影响上传性能，包括节点大小、上传时节点上的其他活动、目标容器是否在 Batch 池所在的同一区域中、将多少个节点同时上传到存储帐户，等等。

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>结合 Batch 文件约定标准使用 Batch 服务 API

使用 Batch 服务 API 保存任务输出时，可以根据喜好为目标容器和 Blob 命名。 也可以选择根据 [Batch 文件约定标准](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)为其命名。 文件约定标准根据作业和任务的名称确定 Azure 存储中用于给定输出文件的目标容器和 Blob 的名称。 如果使用文件约定标准为输出文件命名，则可以在 [Azure 门户](https://portal.azure.com)中查看输出文件。

如果使用 C# 进行开发，则可以使用[适用于 .NET 的 Batch 文件约定库](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files)中内置的方法。 此库将为你创建适当命名的容器和 Blob 路径。 例如，可以调用 API 根据作业名称来获取容器的正确名称：

```csharp
string containerName = job.OutputStorageContainerName();
```

可以使用[CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) 方法返回用于写入容器的共享访问签名 (SAS) URL。 然后，可将此 SAS 传递给 [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) 构造函数。

如果使用除 C# 以外的语言进行开发，则需要自行实现文件约定标准。

## <a name="code-sample"></a>代码示例

[PersistOutputs][github_persistoutputs] 示例项目是 GitHub 上的 [Azure Batch 代码示例][github_samples]之一。 此 Visual Studio 解决方案演示如何使用适用于 .NET 的 Batch 客户端库将任务输出保存到持久性存储。 若要运行该示例，请遵循以下步骤：

1. 在 **Visual Studio 2015 或更新版本**中打开该项目。
2. 将 Batch 和存储**帐户凭据**添加到 Microsoft.Azure.Batch.Samples.Common 项目中的 **AccountSettings.settings**。
3. **生成**（但不要运行）该解决方案。 根据提示还原所有 NuGet 包。
4. 使用 Azure 门户上传 **PersistOutputsTask** 的[应用程序包](batch-application-packages.md)。 在 .zip 包中包含 `PersistOutputsTask.exe` 及其依赖程序集，将应用程序 ID 设置为“PersistOutputsTask”，将应用程序包版本设置为“1.0”。
5. **启动**（运行）**PersistOutputs** 项目。
6. 当系统提示选择用于运行示例的持久性技术时，请输入 **2**，以运行使用 Batch 服务 API 保存任务输出的示例。
7. 如果需要，请输入 **3** 再次运行示例，以使用 Batch 服务 API 保存输出，并根据文件约定标准将目标容器和 Blob 路径命名。

## <a name="next-steps"></a>后续步骤

- 有关使用适用于 .NET 的文件约定库保存任务输出的详细信息，请参阅[使用适用于 .NET 的 Batch 文件约定库将作业和任务数据保存到 Azure 存储](batch-task-output-file-conventions.md)。
- 有关在 Azure Batch 中保存输出数据的其他方法的信息，请参阅[将作业和任务输出保存到 Azure 存储](batch-task-output.md)。

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples

