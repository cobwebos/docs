---
title: "Azure 快速入门 - 使用 .NET 在 Azure 存储中上传、下载和列出 Blob | Microsoft Docs"
description: "在本快速入门中，请创建存储帐户和容器。 然后，使用适用于 .NET 的存储客户端库将一个 Blob 上传到 Azure 存储，下载一个 Blob，然后列出容器中的 Blob。"
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 265691ff189c628156f234083645a4b2ca4b637b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>快速入门：使用 .NET 上传、下载和列出 Blob

本快速入门介绍如何使用 Azure 存储的 .NET 客户端库在容器中上传、下载和列出块 blob。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* 安装 [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) 或 [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x) 版 .NET Core 2.0

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中使用的示例应用程序是基本的控制台应用程序。 

使用 [git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

此命令会将存储库克隆到本地 git 文件夹。 若要打开 Visual Studio 解决方案，请找到 storage-blobs-dotnet-quickstart 文件夹并打开，然后双击 storage-blobs-dotnet-quickstart.sln。 

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

在应用程序中，必须为存储帐户提供连接字符串。 建议将此连接字符串存储在运行应用程序的本地计算机的环境变量中。 根据操作系统按照下面的一个示例创建环境变量。  将 \<yourconnectionstring\> 替换为实际的连接字符串。

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>运行示例

此示例在“我的文档”中创建一个测试文件，将其上传到 Blob 存储，列出容器中的 blob，然后下载具有新名称的文件，以便比较旧文件和新文件。 

导航到应用程序目录，使用 `dotnet run` 命令运行应用程序。

```
dotnet run
```

所示输出类似于以下示例：

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

按任意键继续时，会删除存储容器和文件。 继续前，请在“我的文档”中查看这两个文件。 可以打开它们，并看到它们完全相同。 从控制台窗口复制 blob 的 URL，将其粘贴到浏览器，查看 Blob 存储中的文件的内容。

还可以使用工具（如 [Azure 存储资源管理器](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)）查看 Blob 存储中的文件。 Azure 存储资源管理器是免费的跨平台工具，可用于访问存储帐户信息。 

验证文件后，按任意键可完成演示并删除测试文件。 既然现在已了解此示例的用途，打开 Program.cs 文件可查看代码。 

## <a name="understand-the-sample-code"></a>了解示例代码

接下来逐步介绍示例代码，以便展示其工作方式。

### <a name="get-references-to-the-storage-objects"></a>获取对存储对象的引用

首先创建对用于访问和管理 Blob 存储的对象的引用。 这些对象相互关联，每个对象被列表中的下一个对象使用。

* 创建指向存储帐户的 [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) 对象的实例。

* 创建 [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet) 对象的实例，该对象指向存储帐户中的 Blob 服务。

* 创建 [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) 对象的实例，该对象代表所访问的容器。 容器用于组织 blob，就像使用计算机上的文件夹组织文件一样。

有了 [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) 后，就可以创建 [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet) 对象（该对象指向你感兴趣的特定 blob）的实例，然后执行上传、下载、复制等操作。

> [!IMPORTANT]
> 容器名称必须为小写。 有关容器名称和 blob 名称的详细信息，请参阅[命名和引用容器、Blob 和元数据](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

在本部分中，将创建对象的实例、创建新容器，并对容器设置权限，使 blob 公开，只需 URL 即可对其进行访问。 容器名称为 quickstartblobs。

此示例使用 [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet) ，因为我们想要每次运行示例时都创建新容器。 在整个应用程序中使用相同容器的生产环境中，建议仅调用 **CreateIfNotExists** 一次， 或提前创建容器，这样就无需在代码中创建它。

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 最常用，此快速入门中也使用块 blob。

为了将文件上传到 blob，需获取对目标容器中 blob 的引用。 有了 blob 引用后，可以通过使用 [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) 将数据上传其中。 此操作会创建 blob（若尚不存在），或者覆盖它（若已存在）。

示例代码创建一个用于上传和下载的本地文件，存储作为 fileAndPath 上传的文件和 localFileName 中的 blob 名称。 以下示例将文件上传到名为“quickstartblobs”的容器。

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

Blob 存储支持多种上传方法。 例如，如果有内存流，可以使用 UploadFromStreamAsync 方法，无需使用 UploadFromFileAsync。

块 blob 可以是任何类型的文本或二进制文件。 页 blob 主要用于用于备份 IaaS VM 的 VHD 文件。 追加 blob 用于日志记录，例如有时需要写入到文件，再继续添加更多信息。 存储在 Blob 存储中的大多数对象都是块 blob。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

可以使用 [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) 获取容器中的文件列表。 下面的代码检索 blob 列表，然后循环访问它们，显示找到的 blob 的 URI。 可以从命令窗口中复制 URI，然后将其粘贴到浏览器以查看文件。

如果容器中有 5000 个以下的 blob，调用一次 ListBlobsSegmentedAsync 可检索出所有 blob 名称。 如果容器中的 blob 超过 5000 个，服务会分批检索列表，每组 5000 个 blob，直到检索出所有 blob 名称。 因此，第一次调用此 API 时，它将返回第一组 5,000 个blob 名称和一个继续令牌。 第二次调用时，提供令牌，服务会检索下一组 blob 名称，以此类推，直到继续令牌为 null（表示已检索出所有 blob 名称）。

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>下载 Blob

使用 [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) 将 blob 下载到本地磁盘。

以下代码下载上一部分上传的 blob，对 blob 名称添加“_DOWNLOADED”后缀，以便可以在本地磁盘上看到两个文件。

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>清理资源

如果不再需要此本快速入门中上传的 blob，可使用 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) 删除整个容器。 还可以删除创建的文件（如果不需要）。

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>后续步骤

在此快速入门教程中，介绍了如何使用 .NET 在本地磁盘和 Azure Blob 存储之间传输文件。 要深入了解如何使用 Blob 存储，请继续学习 Blob 存储操作说明。

> [!div class="nextstepaction"]
> [Blob 存储操作说明](storage-dotnet-how-to-use-blobs.md)

有关其他可以下载和运行的 Azure 存储代码示例，请参阅[使用 .NET 的 Azure 存储示例](../common/storage-samples-dotnet.md)列表。

若要详细了解存储资源管理器和 Blob，请参阅[使用存储资源管理器管理 Azure Blob 存储资源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
