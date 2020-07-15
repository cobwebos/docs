---
title: 快速入门：Azure Blob 存储库 v12 - Xamarin
description: 本快速入门介绍如何通过 Xamarin 使用 Azure Blob 存储客户端库版本 12 在 Blob（对象）存储中创建容器和 blob。 接下来介绍如何将 blob 下载到移动设备，以及如何列出容器中的所有 blob。
author: codemillmatt
ms.author: masoucou
ms.date: 05/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b6dc5ba59aa563da6e7c19e6c821f4a9b697832f
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206525"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>快速入门：通过 Xamarin 使用 Azure Blob 存储客户端库 v12

通过 Xamarin 使用 Azure Blob 存储客户端库 v12 入门。 Azure Blob 存储是 Microsoft 提供的适用于云的对象存储解决方案。 请按照步骤操作，安装程序包并试用基本任务的示例代码。 Blob 存储最适合存储巨量的非结构化数据。

通过 Xamarin 使用 Azure Blob 存储客户端库 v12 完成以下操作：

* 创建容器
* 将 blob 上传到 Azure 存储
* 列出容器中所有的 blob
* 将 blob 下载到设备
* 删除容器

参考链接：

* [API 参考文档](/dotnet/api/azure.storage.blobs)
* [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [包 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [示例](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
* Azure 存储帐户 - [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 安装了 [Mobile Development for .NET 工作负荷](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)的 Visual Studio 或 [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>设置
    
本部分逐步讲解如何准备项目，以便通过 Xamarin 使用 Azure Blob 存储客户端库 v12。
    
### <a name="create-the-project"></a>创建项目

1. 打开 Visual Studio 并创建一个空白窗体应用。
1. 将其命名为：BlobQuickstartV12

### <a name="install-the-package"></a>安装包

1. 右键单击“解决方案资源管理器”窗格中的解决方案，并选择“为解决方案管理 NuGet 包”。
1. 搜索“Azure.Storage.Blobs”，将最新稳定版本安装到解决方案的所有项目中。

### <a name="set-up-the-app-framework"></a>设置应用框架

从“BlobQuickstartV12”目录执行以下操作：

1. 在编辑器中打开 MainPage.xaml 文件
1. 删除 `<ContentPage></ContentPage>` 元素之间的所有内容，并将其替换为以下内容：

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>对象模型

Azure Blob 存储最适合存储巨量的非结构化数据。 非结构化数据是不遵循特定数据模型或定义的数据（如文本或二进制数据）。 Blob 存储提供了三种类型的资源：

* 存储帐户
* 存储帐户中的容器
* 容器中的 blob

以下图示显示了这些资源之间的关系。

![Blob 存储体系结构的图示](./media/storage-blobs-introduction/blob1.png)

使用以下 .NET 类与这些资源进行交互：

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient)：`BlobServiceClient` 类可用于操纵 Azure 存储资源和 blob 容器。
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)：`BlobContainerClient` 类可用于操纵 Azure 存储容器及其 blob。
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)：`BlobClient` 类可用于操纵 Azure 存储 blob。
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo)：`BlobDownloadInfo` 类表示从下载 blob 返回的属性和内容。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示了如何在 Xamarin.Forms 应用中使用适用于 .NET 的 Azure Blob 存储客户端库执行以下任务：

* [创建类级别变量](#create-class-level-variables)
* [创建容器](#create-a-container)
* [将 blob 上传到容器中](#upload-blobs-to-a-container)
* [列出容器中的 blob](#list-the-blobs-in-a-container)
* [下载 blob](#download-blobs)
* [删除容器](#delete-a-container)

### <a name="create-class-level-variables"></a>创建类级别变量

以下代码声明了几个类级别变量。 在本示例的其余部分中，需要使用这些变量与 Azure Blob 存储通信。

除了在[配置存储连接字符串](#configure-your-storage-connection-string)部分中设置的存储帐户的连接字符串外，还需要这些变量。

请将此代码作为类级别变量添加到 MainPage.xaml.cs 文件中：

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>创建容器

确定新容器的名称。 以下代码将 GUID 值追加到容器名称，确保其是唯一的。

> [!IMPORTANT]
> 容器名称必须为小写。 有关命名容器和 Blob 的详细信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

创建 [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) 类的实例。 然后，调用 [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) 方法在存储帐户中创建容器。

将以下代码添加到 MainPage.xaml.cs 文件中：

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>将 blob 上传到容器中

以下代码片段：

1. 创建文本的 `MemoryStream`。
1. 通过调用 [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) 类的 [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync?view=azure-dotnet#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) 函数，将文本上传到 Blob，并向其传递文件名以及文本的 `MemoryStream`。 此方法将创建 Blob（如果该 Blob 尚不存在），或者覆盖 Blob（如果该 Blob 已存在）。

将以下代码添加到 MainPage.xaml.cs 文件中：

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

通过调用 [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) 方法，列出容器中的 blob。 在这种情况下，只向容器添加了一个 blob，因此列表操作只返回那个 blob。

将以下代码添加到 MainPage.xaml.cs 文件中：

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>下载 Blob

通过调用 [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) 方法，下载以前创建的 blob。 此示例代码先将 blob 的 `Stream` 表示形式复制到 `MemoryStream` 中，然后再复制到 `StreamReader` 中，以便显示文本。

将以下代码添加到 MainPage.xaml.cs 文件中：

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>删除容器

以下代码使用 [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) 来删除整个容器，从而清除该应用所创建的资源。

在删除 blob 和容器之前，应用首先会提示你进行确认。 可以通过此机会验证是否已正确创建资源，然后再删除这些资源。

将以下代码添加到 MainPage.xaml.cs 文件中：

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>运行代码

应用启动时，会首先创建容器，就像所显示的那样。 然后，你需要单击相关按钮以上传、列出、下载 Blob 以及删除容器。

若要在 Windows 上运行应用，请按 F5。 若要在 Mac 上运行应用，请按 Cmd+Enter。

每次操作后，应用都会写入屏幕。 应用的输出类似于以下示例：

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

在开始清理过程之前，请验证屏幕上 blob 内容的输出是否与上传的值匹配。

验证值后，确认删除容器的提示并完成演示。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何通过 Xamarin 使用 Azure Blob 存储客户端库 v12 上传、下载和列出 blob。

若要查看 Blob 存储示例应用，请继续执行以下操作：

> [!div class="nextstepaction"]
> [Azure Blob 存储 SDK v12 Xamarin 示例](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* 有关教程、示例、快速入门和其他文档，请访问[面向移动开发人员的 Azure](/azure/mobile-apps)。
* 若要详细了解 Xamarin，请参阅 [Xamarin 入门](/xamarin/get-started/)。
