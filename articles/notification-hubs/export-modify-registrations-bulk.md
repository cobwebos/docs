---
title: 导出和导入大容量中的 Azure 通知中心注册 |Microsoft Docs
description: 了解如何使用通知中心批量支持，可执行大量的通知中心的操作或导出所有注册信息。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: a86c3bd85f9d611787a41754f49ee2475ba33a9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58175763"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>导出和导入大容量中的 Azure 通知中心注册
有在其中需要创建或修改大量注册信息在通知中心的方案。 其中一些方案是标记更新批处理计算后或迁移为使用通知中心的现有推送实现。

本文介绍如何执行大量的通知中心的操作或导出所有注册，在大容量。

## <a name="high-level-flow"></a>高级流程
批处理支持旨在支持长时间运行的作业涉及数百万条注册信息。 为了达到此规模，批处理支持使用 Azure 存储来存储作业详细信息和输出。 以执行大容量更新操作的用户是所需的 blob 容器，其内容是注册更新操作的列表中创建文件。 当正在启动作业，用户提供输入的 blob，以及指向输出目录 （也在 blob 容器中） 的 URL 的 URL。 启动作业后，用户可以通过查询开始作业中提供的 URL 位置检查状态。 特定作业只能执行特定类型的操作 （创建、 更新或删除）。 与执行导出操作。

## <a name="import"></a>导入

### <a name="set-up"></a>设置
本部分假定您有以下实体：

- 预配的通知中心。
- Azure 存储 blob 容器。
- 对 Azure 存储和 Azure 服务总线 NuGet 包的引用。

### <a name="create-input-file-and-store-it-in-a-blob"></a>创建输入的文件并将其存储在 blob 中
输入的文件包含在 XML 中，每行表示一个序列化注册信息列表。 使用 Azure SDK，下面的代码示例演示如何序列化注册并将其上载到 blob 容器。

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> 前面的代码序列化在内存中的注册，然后将整个流上载到 blob。 如果已上传超过几兆字节的文件，请参阅 Azure blob 指南如何执行以下步骤：例如，[块 blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)。

### <a name="create-url-tokens"></a>创建 URL 标记
输入的文件上传后, 生成的 Url 提供给你的通知中心以获得输入的文件和输出目录。 输入和输出，可以使用两个不同的 blob 容器。

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>提交作业
使用两个输入和输出 Url，现在可以开始批处理作业。

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

此示例创建输入和输出 Url 中，除了`NotificationHubJob`对象，其中包含`JobType`对象，可以是以下类型之一：

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

后调用完成，该作业将继续运行通知中心，您可以检查其状态对的调用[GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet)。

在作业完成时，可以通过查看输出目录中的以下文件中检查结果：

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

这些文件包含从您的批的成功和失败操作的列表。 文件格式是`.cvs`，其中每行具有原始输入文件的行号和操作 （通常创建或更新的注册说明） 的输出中。

### <a name="full-sample-code"></a>完整的示例代码
下面的示例代码导入的通知中心注册。

```csharp
using Microsoft.ServiceBus.Notifications;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>导出
导出注册信息类似于导入具有以下差异：

- 您只需要输出 URL。
- 创建类型 ExportRegistrations NotificationHubJob。

### <a name="sample-code-snippet"></a>示例代码片段
下面是示例代码片段用于导出在 Java 中的注册：

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>后续步骤
若要了解有关注册的详细信息，请参阅以下文章：

- [管理注册](notification-hubs-push-notification-registration-management.md)
- [注册标记](notification-hubs-tags-segment-push-message.md)
- [模板注册](notification-hubs-templates-cross-platform-push-messages.md)
