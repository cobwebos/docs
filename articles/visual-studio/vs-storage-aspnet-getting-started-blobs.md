---
title: 开始使用 Azure Blob 存储和 Visual Studio 连接服务 (ASP.NET) | Microsoft Docs
description: 在使用 Visual Studio 连接服务连接到存储帐户后，如何开始在 Visual Studio 中的 ASP.NET 项目中使用 Azure Blob 存储
services: storage
author: ghogen
manager: douge
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: 55b083ac5384e749098338d2f3b7b24bfe16fe8f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696559"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>开始使用 Azure Blob 存储和 Visual Studio 连接服务 (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob 存储是一种将非结构化数据作为对象或 Blob 存储在云中的服务。 Blob 存储可以存储任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。 Blob 存储也称为对象存储。

本教程介绍如何针对使用 Blob 存储的一些常见方案编写 ASP.NET 代码。 方案包括创建 Blob 容器，以及上传、列出、下载和删除 Blob。

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>先决条件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>创建 MVC 控制器 

1. 在**解决方案资源管理器**中，右键单击“控制器”。

2. 从上下文菜单中，选择“添加” > “控制器”。

    ![解决方案资源管理器的屏幕截图，其中突出显示了“添加”和“控制器”](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. 在“添加基架”对话框中，选择“MVC 5 控制器 - 空”，并选择“添加”。

    ![“添加基架”对话框的屏幕截图](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. 在“添加控制器”对话框中，将控制器命名为“BlobsController”，然后选择“添加”。

    ![“添加控制器”对话框的屏幕截图](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. 将以下 `using` 指令添加到 `BlobsController.cs` 文件：

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>连接到存储帐户并获取容器引用

Blob 容器是由 Blob 和文件夹组成的嵌套式层次结构。 本文档中的剩余步骤需要引用 Blob 容器，以便在其自身的方法中放置代码来实现可重用性。

以下步骤创建一个方法用于使用 **Web.config** 中的连接字符串连接到存储帐户。这些步骤还创建对容器的引用。  **Web.config** 中的连接字符串设置是以 `<storageaccountname>_AzureStorageConnectionString` 格式命名的。 

1. 打开 `BlobsController.cs` 文件。

1. 添加一个名为 **GetCloudBlobContainer**、可返回 **CloudBlobContainer** 的方法。  请务必将`<storageaccountname>_AzureStorageConnectionString` 替换为 **Web.config** 中的键的实际名称。
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> 即使 *test-blob-container* 尚不存在，此代码也会创建对它的引用。 这样便可以使用下一步中所示的 `CreateIfNotExists` 方法创建容器。

## <a name="create-a-blob-container"></a>创建 Blob 容器

以下步骤演示了如何创建 Blob 容器：

1. 添加一个名为 `CreateBlobContainer`、可返回 `ActionResult` 的方法。

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. 获取表示所需 Blob 容器名称引用的 `CloudBlobContainer` 对象。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. 调用 `CloudBlobContainer.CreateIfNotExists` 方法来创建容器（如果容器尚不存在）。 如果容器原本不存在，但已成功创建，则 `CloudBlobContainer.CreateIfNotExists` 方法返回 **true**。 否则，该方法返回 **false**。    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. 使用 Blob 容器的名称更新 `ViewBag`。

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    下面显示了已完成的 `CreateBlobContainer` 方法：

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. 在**解决方案资源管理器**中，右键单击“视图”文件夹。

2. 从上下文菜单中，选择“添加” > “新建文件夹”。 将新文件夹命名为 *Blobs*。 
 
1. 在**解决方案资源管理器**中，展开“视图”文件夹并右键单击“Blob”。

4. 从上下文菜单中，选择“添加” > “视图”。

1. 在“添加视图”对话框中，输入 **CreateBlobContainer** 作为视图名称，并选择“添加”。

1. 打开 `CreateBlobContainer.cshtml`，并按以下代码片段所示对其进行修改：

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. 在**解决方案资源管理器**中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. 运行应用程序，并选择“创建 Blob 容器”，可以看到与以下屏幕截图类似的结果：
  
    ![“创建 blob 容器”的屏幕截图](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    如前所述，仅当容器原本不存在但已创建时，`CloudBlobContainer.CreateIfNotExists` 方法才返回 **true**。 因此，如果在容器存在的情况下运行该应用，该方法会返回 **false**。

## <a name="upload-a-blob-into-a-blob-container"></a>将 Blob 上传到 Blob 容器中

[创建 Blob 容器](#create-a-blob-container)之后，将文件上传到该容器中。 本部分逐步讲解如何将本地文件上传到 Blob 容器。 相关步骤假设已有一个名为 *test-blob-container* 的 Blob 容器。 

1. 打开 `BlobsController.cs` 文件。

1. 添加一个名为 `UploadBlob`、可返回字符串的方法。

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. 在 `UploadBlob` 方法中，获取表示所需 Blob 容器名称引用的 `CloudBlobContainer` 对象。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure 存储支持不同的 Blob 类型。 本教程使用的是块 Blob。 若要检索对块 Blob 的引用，请调用 `CloudBlobContainer.GetBlockBlobReference` 方法。

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Blob 名称是用于检索 Blob 的 URL 的一部分，可以是任意字符串（包括文件的名称）。

1. 有了 Blob 引用之后，可通过调用 Blob 引用对象的 `UploadFromStream` 方法，将任何数据流上传到该引用。 `UploadFromStream` 方法创建 Blob（如果不存在）或者覆盖它（如果存在）。 （将 *&lt;file-to-upload>* 更改为要上传的文件的完全限定路径。）

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    下面演示了已完成的 `UploadBlob` 方法（包含要上传的文件的完全限定路径）：

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. 在**解决方案资源管理器**中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. 运行该应用程序，并选择“上传 Blob”。  此时应显示文字“success!” 。
    
    ![成功验证的屏幕截图](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>列出 Blob 容器中的 Blob

本部分说明如何列出 Blob 容器中的 Blob。 示例代码引用在[创建 Blob 容器](#create-a-blob-container)部分创建的 test-blob-container。

1. 打开 `BlobsController.cs` 文件。

1. 添加一个名为 `ListBlobs`、可返回 `ActionResult` 的方法。

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. 在 `ListBlobs` 方法中，获取表示 Blob 容器引用的 `CloudBlobContainer` 对象。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. 若要列出 Blob 容器中的 Blob，请使用 `CloudBlobContainer.ListBlobs` 方法。 `CloudBlobContainer.ListBlobs` 方法返回一个 `IListBlobItem` 对象，该对象可强制转换为`CloudBlockBlob`、`CloudPageBlob` 或 `CloudBlobDirectory` 对象。 以下代码片段枚举 blob 容器中的所有 blob。 每个 blob 均可基于其类型强制转换为相应的对象。 其名称（对于 **CloudBlobDirectory** 则为 URI）将添加到列表中。

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    除了 Blob，Blob 容器还可以包含目录。 假设存在一个名为 *test-blob-container* 并具有以下层次结构的 Blob 容器：

        foo.png
        dir1/bar.png
        dir2/baz.png

    **blob** 字符串列表包含的值如下所示（使用前面的代码示例）：

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    可以看到，该列表仅包含顶层实体，而不包含嵌套实体（*bar.png* 和 *baz.png*）。 若要列出 Blob 容器中的所有实体，请更改代码，以便在 **CloudBlobContainer.ListBlobs** 方法中为 **useFlatBlobListing** 参数传递 **true**。    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    将 **useFlatBlobListing** 参数设置为 **true** 会返回一个平面列表，其中包含 Blob 容器中的所有实体。 这会生成以下结果：

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    下面显示了已完成的 **ListBlobs** 方法：

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. 在**解决方案资源管理器**中，展开“视图”文件夹并右键单击“Blob”。

2. 从上下文菜单中，选择“添加” > “视图”。

1. 在“添加视图”对话框中，输入 `ListBlobs` 作为视图名称，并选择“添加”。

1. 打开 `ListBlobs.cshtml`，将内容替换为以下代码：

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. 在**解决方案资源管理器**中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. 运行应用程序，并选择“列出 blob”，可以看到与以下屏幕截图类似的结果：
  
    ![“列出 blob”的屏幕截图](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>下载 Blob

本部分演示了如何下载 blob。 可以将其保存到本地存储中或将其内容读取到一个字符串中。 示例代码引用在[创建 Blob 容器](#create-a-blob-container)部分创建的 test-blob-container。

1. 打开 `BlobsController.cs` 文件。

1. 添加一个名为 `DownloadBlob`、可返回字符串的方法。

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. 在 `DownloadBlob` 方法中，获取表示 Blob 容器引用的 `CloudBlobContainer` 对象。
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. 调用 `CloudBlobContainer.GetBlockBlobReference` 方法以获取 Blob 引用对象。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. 若要下载 Blob，请使用 `CloudBlockBlob.DownloadToStream` 方法。 以下代码将 blob 的内容传输到一个流对象。 然后将该对象保存到一个本地文件中。 （将 *&lt;local-file-name>* 更改为表示要将 Blob 下载到的位置的完全限定文件名。） 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    下面演示了已完成的 `ListBlobs` 方法（包含所要创建的本地文件的完全限定路径）：
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. 在**解决方案资源管理器**中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. 运行该应用程序，并选择“下载 Blob”下载 Blob。 `CloudBlobContainer.GetBlockBlobReference` 方法调用中指定的 Blob 会将内容下载到 `File.OpenWrite` 方法调用中指定的位置。  浏览器中应会显示 文字“success!”。 

## <a name="delete-blobs"></a>删除 Blob

以下步骤演示如何删除 Blob：

1. 打开 `BlobsController.cs` 文件。

1. 添加一个名为 `DeleteBlob`、可返回字符串的方法。

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. 在 `DeleteBlob` 方法中，获取表示 Blob 容器引用的 `CloudBlobContainer` 对象。
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. 调用 `CloudBlobContainer.GetBlockBlobReference` 方法以获取 Blob 引用对象。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. 若要删除 Blob，请使用 `Delete` 方法。

    ```csharp
    blob.Delete();
    ```
    
    已完成的 `DeleteBlob` 方法应如下所示：
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. 在**解决方案资源管理器**中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在最后一个 **Html.ActionLink** 的后面，添加以下 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. 运行该应用程序，并选择“删除 Blob”删除 `CloudBlobContainer.GetBlockBlobReference` 方法调用中指定的 Blob。 浏览器中应会显示 文字“success!”。 选择浏览器的“后退”按钮，并选择“列出 Blob”以验证该 Blob 是否不再在容器中。

## <a name="next-steps"></a>后续步骤

本教程已介绍了如何使用 ASP.NET 在 Azure 存储中存储、列出和检索 Blob。 查看更多功能指南，以了解在 Azure 中存储数据的其他方式。

  * [开始使用 Azure 表存储和 Visual Studio 连接服务 (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [开始使用 Azure 队列存储和 Visual Studio 连接服务 (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
