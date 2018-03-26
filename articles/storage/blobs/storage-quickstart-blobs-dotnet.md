---
title: Azure 快速入门 - 使用 .NET 在 Azure 存储中上传、下载和列出 Blob | Microsoft Docs
description: 在本快速入门中，请创建存储帐户和容器。 然后，使用适用于 .NET 的存储客户端库将一个 Blob 上传到 Azure 存储，下载一个 Blob，然后列出容器中的 Blob。
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: 716e61840f4bfb5a68a995683e67dae0b43d3854
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>快速入门：使用 .NET 上传、下载和列出 Blob

本快速入门介绍如何使用 Azure 存储的 .NET 客户端库在容器中上传、下载和列出块 blob。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成此快速入门，请首先在 [Azure 门户](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)中创建 Azure 存储帐户。 有关如何创建帐户的帮助，请参阅[创建存储帐户](../common/storage-quickstart-create-account.md)。

接下来，请下载并安装适用于操作系统的 .NET Core 2.0。 也可选择安装一个可以在操作系统中使用的编辑器。

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- 安装[用于 Windows 的 .NET Core](https://www.microsoft.com/net/download/windows/build) 
- （可选）安装[用于 Windows 的 Visual Studio](https://www.visualstudio.com/) 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- 安装[用于 Linux 的 .NET Core](https://www.microsoft.com/net/download/linux/build)
- （可选）安装 [Visual Studio Code](https://www.visualstudio.com/) 和 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- 安装[用于 macOS 的 .NET Core](https://www.microsoft.com/net/download/macos/build)。
- （可选）安装[用于 Mac 的 Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/)

---

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中使用的示例应用程序是基本的控制台应用程序。 可以浏览 [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart) 上的示例应用程序。

使用 [git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

此命令会将存储库克隆到本地 git 文件夹。 若要打开 Visual Studio 解决方案，请找到 storage-blobs-dotnet-quickstart 文件夹并打开，然后双击 storage-blobs-dotnet-quickstart.sln。 

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

若要运行应用程序，必须为存储帐户提供连接字符串。 可以将此连接字符串存储在运行应用程序的本地计算机的环境变量中。 使用以下某个命令创建环境变量，具体取决于操作系统。 将 `<yourconnectionstring>` 替换为实际的连接字符串。

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

编辑 .bash_profile，然后添加环境变量：

```
export STORAGE_CONNECTION_STRING=
```

添加环境变量后，请先注销，然后再登录回来，使更改生效。 也可通过终端键入“source .bash_profile”。

---

## <a name="run-the-sample"></a>运行示例

此示例在本地 **MyDocuments** 文件夹中创建一个测试文件，然后将其上传到 Blob 存储。 此示例然后会列出容器中的 Blob，并使用新名称下载文件，以便对旧文件和新文件进行比较。 

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

按 **Enter** 键时，应用程序会删除存储容器和文件。 在删除之前，请在 **MyDocuments** 文件夹中查看这两个文件。 可以打开它们，然后就会观察到它们完全相同。 从控制台窗口复制 Blob 的 URL，将其粘贴到浏览器中，查看 Blob 的内容。

验证文件后，按任意键可完成演示并删除测试文件。 既然现在已了解此示例的用途，打开 Program.cs 文件可查看代码。 

## <a name="understand-the-sample-code"></a>了解示例代码

接下来介绍示例代码，探讨其工作方式。

### <a name="try-parsing-the-connection-string"></a>尝试分析连接字符串

此示例所执行的第一项操作是检查环境变量是否包含一个连接字符串，该字符串在经过分析后可以创建一个指向存储帐户的 [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) 对象。 若要检查连接字符串是否有效，请使用 [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) 方法。 如果 **TryParse** 成功，它会初始化 *storageAccount* 变量并返回 **true**。

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring", EnvironmentVariableTarget.User);

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>创建容器并设置权限

接下来，此示例创建一个容器并设置其权限，使容器中的任何 Blob 都是公开的。 如果某个 Blob 是公开的，则任何客户端都可以对其进行匿名访问。

若要创建容器，请先创建 [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) 对象的实例，该对象指向存储帐户中的 Blob 存储。 接下来，请创建 [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) 对象的实例，然后创建容器。 

在此示例中，示例调用 [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) 方法来创建容器。 GUID 值会追加到容器名称，确保其是唯一的。 在生产环境中，通常情况下，首选使用 [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) 方法来创建容器的前提是该方法不存在，以免出现名称冲突。

> [!IMPORTANT]
> 容器名称必须为小写。 有关命名容器和 Blob 的详细信息，请参阅[命名和引用容器、Blob 和元数据](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

接下来，示例将本地文件上传到块 Blob。 代码示例在需要获取 **CloudBlockBlob** 对象的引用时，会针对前面部分创建的容器调用 [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) 方法。 然后，它会通过调用 [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) 方法将所选文件上传到 Blob。 此方法将创建 Blob（如果该 Blob 尚不存在），或者覆盖 Blob（如果该 Blob 已存在）。 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

示例使用 [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) 方法列出容器中的 Blob。 就此示例来说，只向容器添加了一个 Blob，因此列表操作只返回那个 Blob。

如果一次调用需要返回的 Blob 数过多（默认为超过 5000 个），则 **ListBlobsSegmentedAsync** 方法会返回总结果集的一部分和一个继续令牌。 若要检索下一部分的 Blob，请提供前一调用返回的继续令牌，依此类推，直至继续令牌为 null。 继续令牌为 null 表明已检索所有 Blob。 示例代码演示了如何根据最佳做法使用继续令牌。

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>下载 Blob

接下来，示例使用 [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) 方法将以前创建的 Blob 下载到本地文件系统。 示例代码向 Blob 名称添加后缀“_DOWNLOADED”，这样你就可以在本地文件系统中看到这两个文件。

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>清理资源

示例可以使用 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) 来删除整个容器，从而清除所创建的资源。 也可根据需要删除本地文件。

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

## <a name="resources-for-developing-net-applications-with-blobs"></a>用于通过 Blob 开发 .NET 应用程序的资源

请查看以下其他资源，了解如何通过 Blob 存储进行 .NET 开发：

### <a name="binaries-and-source-code"></a>二进制文件和源代码

- 下载 Azure 存储的最新版 [.NET 客户端库](https://www.nuget.org/packages/WindowsAzure.Storage/)的 NuGet 包。 
- 查看 GitHub 上的 [.NET 客户端库源代码](https://github.com/Azure/azure-storage-net)。

### <a name="client-library-reference-and-samples"></a>客户端库参考和示例

- 参阅 [.NET API 参考](https://docs.microsoft.com/dotnet/api/overview/azure/storage)，详细了解 .NET 客户端库。
- 浏览使用 .NET 客户端库编写的 [Blob 存储示例](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 .NET 上传、下载和列出 Blob。 

若要了解如何创建一个 Web 应用，以便将映像上传到 Blob 存储，请继续学习[使用 Azure 存储将映像数据上传到云中](storage-upload-process-images.md)。

> [!div class="nextstepaction"]
> [Blob 存储操作说明](storage-dotnet-how-to-use-blobs.md)

- 若要详细了解 .NET Core，请参阅 [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/)（.NET 10 分钟入门）。
- 若要了解可以通过用于 Windows 的 Visual Studio 部署的示例应用程序，请参阅 [.NET Photo Gallery Web Application Sample with Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/)（.NET 照片库 Web 应用程序示例与 Azure Blob 存储）。
 