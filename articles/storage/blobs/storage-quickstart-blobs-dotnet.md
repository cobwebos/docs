---
title: "Azure 快速入门 - 使用 .NET 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象 | Microsoft Docs"
description: "快速了解如何使用 .NET 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: robinsh
ms.openlocfilehash: 97bacc2c1285fe4a467a54f224bb9fabbd851fee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>使用 .NET 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象

本快速入门介绍如何使用 C#.NET 在 Windows 中上传、下载和列出 Azure Blob 存储的容器中的块 blob。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)：
    - **Azure 开发**

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-storage-account-using-the-azure-portal"></a>使用 Azure 门户创建存储帐户

首先，创建用于本快速入门的新通用存储帐户。 

1. 转到 [Azure 门户](https://portal.azure.com)，使用 Azure 帐户登录。 
2. 在“中心”菜单上，选择“新建” > “存储” > “存储帐户 - blob、文件、表、队列”。 
3. 输入存储帐户的名称。 名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 不能与其他名称重复。
4. 将 `Deployment model` 设置为“资源管理器”。
5. 将“`Account kind`”设置为“常规用途”。
6. 将“`Performance`”设置为“标准”。 
7. 将“`Replication`”设置为“本地冗余存储(LRS)”。
8. 将“`Storage service encryption`”设置为“禁用”。
9. 将“`Secure transfer required`”设置为“禁用”。
10. 选择订阅。 
11. 对于“`resource group`”，创建一个新资源组，并为其赋予唯一名称。 
12. 选择要用于存储帐户的“`Location`”。
13. 选中“固定到仪表板”，然后单击“创建”，创建存储帐户。 

创建存储帐户后，它被固定到仪表板。 单击可将其打开。 在“设置”下，单击“访问密钥”。 选择一个密钥并将连接字符串复制到剪贴板，然后将其粘贴到记事本，以供将来使用。

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中使用的示例应用程序是基本的控制台应用程序。 

使用 [git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

此命令会将存储库克隆到本地 git 文件夹。 若要打开 Visual Studio 解决方案，请找到 storage-blobs-dotnet-quickstart 文件夹并打开，然后双击 storage-blobs-dotnet-quickstart.sln。 

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

在应用程序中，必须为存储帐户提供连接字符串。 从 Visual Studio 中的解决方案资源管理器打开 `app.config` 文件。 找到 StorageConnectionString 条目。 对于“值”，请将整个连接字符串值替换为记事本中从 Azure 门户保存的值。 完成后，应会看到类似于下面的信息。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=youraccountname;
    AccountKey=7NGE5jasdfdRzASDFNOMEx1u20W/thisisjustC/anexampleZK/Rt5pz2xNRrDckyv8EjB9P1WGF==" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>运行示例

此示例在“我的文档”中创建一个测试文件，将其上传到 Blob 存储，列出容器中的 blob，然后下载具有新名称的文件，以便比较旧文件和新文件。 

按 F5 运行示例。 控制台窗口中显示的输出与下面的内容类似： 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

按任意键继续时，会删除存储容器和文件。 继续下一步操作前，请检查“我的文档”中的两个文件 - 可以打开它们，查看它们是否相同。 从控制台窗口复制 blob 的 URL，将其粘贴到浏览器，查看 Blob 存储中的文件的内容。

还可以使用工具（如 [Azure 存储资源管理器](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)）查看 Blob 存储中的文件。 Azure 存储资源管理器是免费的跨平台工具，可用于访问存储帐户信息。 

验证文件后，按任意键可完成演示并删除测试文件。 既然现在已了解此示例的用途，打开 Program.cs 文件可查看代码。 

## <a name="get-references-to-the-storage-objects"></a>获取对存储对象的引用

首先创建对用于访问和管理 Blob 存储的对象的引用。 这些对象相互关联 - 每个对象被列表中的下一个对象使用。

* 实例化指向存储帐户的 CloudStorageAccount 对象。 

* 实例化 CloudBlobClient 对象，该对象指向存储帐户中的 Blob 服务。 

* 实例化 CloudBlobContainer 对象，该对象代表你正在访问的容器。 容器用于组织 blob，就像使用计算机上的文件夹组织文件一样。

有了 CloudBlobContainer 后，可以实例化 CloudBlockBlob 对象（该对象指向你感兴趣的特定 blob），然后执行上传、下载、复制等操作。

本部分中，将实例化对象、创建新容器，并对容器设置权限，使 blob 公开，只需 URL 即可对其进行访问。 容器名称为 quickstartblobs。 

此示例使用 CreateIfNotExists，因为我们想要每次运行示例时创建新容器。 在应用程序中使用相同容器的生产环境中，建议仅调用 CreateIfNotExists 一次，或提前创建容器，这样就无需在代码中创建它。

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

## <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 最常用，此快速入门中也使用块 blob。 

为了将文件上传到 blob，需获取对目标容器中 blob 的引用。 有了 blob 引用后，可以通过使用 [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) 将数据上传其中。 此操作会创建 blob（若尚不存在），或者覆盖它（若已存在）。

示例代码创建一个用于上传和下载的本地文件，存储作为 fileAndPath 上传的文件和 localFileName 中的 blob 名称。 以下示例将文件上传到名为“quickstartblobs”的容器。

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Blob 存储支持多种上传方法。 例如，如果有内存流，可以使用 UploadFromStreamAsync 方法，无需使用 UploadFromFileAsync。 

块 blob 最大可以为 4.7 TB，并且可以是从 Excel 电子表格到大视频文件的任何内容。 页 blob 主要用于用于备份 IaaS VM 的 VHD 文件。 追加 blob 用于日志记录，例如有时需要写入到文件，再继续添加更多信息。 存储在 Blob 存储中的大多数对象都是块 blob。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) 获取容器中的文件列表。 下面的代码检索 blob 列表，然后循环访问它们，显示找到的 blob 的 URI。 可以从命令窗口中复制 URI，然后将其粘贴到浏览器以查看文件。

如果容器中有 5000 个以下的 blob，调用一次 ListBlobsSegmentedAsync 可检索出所有 blob 名称。 如果容器中的 blob 超过 5000 个，服务会分批检索列表，每组 5000 个 blob，直到检索出所有 blob 名称。 因此，第一次调用此 API 时，它将返回第一组 5,000 个blob 名称和一个继续令牌。 第二次调用时，提供令牌，服务会检索下一组 blob 名称，以此类推，直到继续令牌为 null（表示已检索出所有 blob 名称）。 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

## <a name="download-blobs"></a>下载 Blob

使用 [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) 将 blob 下载到本地磁盘。

以下代码下载上一部分上传的 blob，对 blob 名称添加“_DOWNLOADED”后缀，以便可以在本地磁盘上看到两个文件。 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要此本快速入门中上传的 blob，可使用 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) 删除整个容器。 还可以删除创建的文件（如果不需要）。

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>后续步骤

在此快速入门教程中，介绍了如何使用 .NET 在本地磁盘和 Azure Blob 存储之间传输文件。 要深入了解如何使用 Blob 存储，请继续学习 Blob 存储操作说明。

> [!div class="nextstepaction"]
> [Blob 存储操作说明](storage-dotnet-how-to-use-blobs.md)

若要详细了解存储资源管理器和 Blob，请参阅[使用存储资源管理器管理 Azure Blob 存储资源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
