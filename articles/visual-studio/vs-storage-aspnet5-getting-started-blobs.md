---
title: 开始使用 blob 存储和 Visual Studio 连接服务 (ASP.NET Core) | Microsoft Docs
description: 在使用 Visual Studio 连接服务创建存储帐户后，如何开始在 Visual Studio ASP.NET Core 项目中使用 Azure Blob 存储
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 566e2edc0157ccd02e0b44ae7df86c4b484858b0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793947"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>开始使用 Azure Blob 存储和 Visual Studio 连接服务 (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

本文介绍通过使用 Visual Studio 中的“连接服务”功能在 ASP.NET Core 项目中创建或引用 Azure 存储帐户之后，如何开始在 Visual Studio 中使用 Azure Blob 存储。 执行“连接服务”操作会安装相应的 NuGet 程序包，以访问项目中的 Azure 存储，并将存储帐户的连接字符串添加到项目配置文件中。 （有关 Azure 存储的常规信息，请参阅[存储文档](https://azure.microsoft.com/documentation/services/storage/)。）

Azure Blob 存储是一项可存储大量非结构化数据的服务，用户可在世界任何地方通过 HTTP 或 HTTPS 访问这些数据。 单个 Blob 可以是任意大小。 Blob 可以是图像、音频和视频文件、原始数据以及文档文件等。 本文介绍通过使用 Visual Studio 中的“连接服务”在 ASP.NET Core 项目中创建 Azure 存储帐户之后，如何开始使用 blob 存储。

正如文件位于文件夹中一样，存储 Blob 位于容器中。 创建 blob 后，可以在此 blob 中创建一个或多个容器。 例如，在名为“Scrapbook”的 blob 中，可以创建名为“images”的容器，用于存储图片，以及另一个名为“audio”的容器，用于存储音频文件。 创建这些容器后，即可将各个文件上传到其中。 有关以编程方式操作 Blob 的详细信息，请参阅[快速入门：使用 .NET 上传、下载和列出 Blob](../storage/blobs/storage-quickstart-blobs-dotnet.md)。

某些 Azure 存储 API 为异步，而本文中的代码假定正在使用异步方法。 有关详细信息，请参阅[异步编程](https://docs.microsoft.com/dotnet/csharp/async)。

## <a name="access-blob-containers-in-code"></a>使用代码访问 blob 容器

若要以编程方式访问 ASP.NET Core 项目中的 blob，需要添加以下代码（如果尚未存在）：

1. 添加必要的 `using` 语句：

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. 获取表示存储帐户信息的 `CloudStorageAccount` 对象。 使用下面的代码从 Azure 服务配置中获取存储连接字符串和存储帐户信息：

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. 使用 `CloudBlobClient` 对象获取对存储帐户中现有容器的 `CloudBlobContainer` 引用：

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>使用代码创建容器

还可以通过调用 `CreateIfNotExistsAsync` 使用 `CloudBlobClient` 在存储帐户中创建容器：

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

如果要让容器中的文件可供所有人使用，可将容器设置为公用：

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>将 Blob 上传到容器中

要将 Blob 文件上传到容器中，请获取容器引用，并使用它来获取 Blob 引用。 然后通过调用 `UploadFromStreamAsync` 方法将任何数据流上传到该引用。 此操作将创建 blob（如果该 blob 不存在），并覆盖现有 blob。 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

若要列出容器中的 blob，首先获取容器引用，然后调用其 `ListBlobsSegmentedAsync` 方法来检索其中的 blob 和/或目录。 若要访问返回的 `IListBlobItem` 的丰富属性集和方法，请将它强制转换为 `CloudBlockBlob`、`CloudPageBlob` 或 `CloudBlobDirectory` 对象。 如果 blob 类型未知，可以使用类型检查来确定要将其强制转换为哪种类型。

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

有关列出 Blob 容器内容的其他方法，请参阅[快速入门：使用 .NET 上传、下载和列出 Blob](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)。

## <a name="download-a-blob"></a>下载 Blob

要下载某个 blob，请首先获取对该 blob 的引用，然后调用 `DownloadToStreamAsync` 方法。 下面的示例使用 `DownloadToStreamAsync` 方法将 blob 内容传输到稍后可以另存为本地文件的流对象。

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

有关将 Blob 另存为文件的其他方法，请参阅[快速入门：使用 .NET 上传、下载和列出 Blob](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)。

## <a name="delete-a-blob"></a>删除 Blob

要删除某个 blob，请首先获取对该 blob 的引用，然后调用 `DeleteAsync` 方法：

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
