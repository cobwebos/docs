---
title: "Azure Blob 存储和 Visual Studio 连接服务 (ASP.NET Core) 入门 | Microsoft Docs"
description: "使用 Visual Studio 连接服务连接到存储帐户之后，如何在 Visual Studio 的 ASP.NET Core 项目中开始使用 Azure Blob 存储"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 42390effd6a2d2a8afe9350e0a77d3c0a17b6129
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>开始使用 Azure Blob 存储和 Visual Studio 连接服务 (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob 存储是一种将非结构化数据作为对象或 Blob 存储在云中的服务。 Blob 存储可以存储任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。 Blob 存储也称为对象存储。

本教程介绍如何针对使用 Blob 存储的一些常见方案编写 ASP.NET Core 代码。 方案包括创建 Blob 容器，以及上传、列出、下载和删除 Blob。

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>系统必备

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>设置开发环境

本部分将指导完成开发环境设置。 这包括：创建 ASP.NET 模型-视图-控制器 (MVC) 应用、添加“连接服务”连接、添加控制器，以及指定所需的命名空间指令。

### <a name="create-an-aspnet-mvc-app-project"></a>创建 ASP.NET MVC 应用项目

1. 打开 Visual Studio。

1. 在主菜单中，选择“文件” > “新建” > “项目”。

1. 在“新建项目”对话框中，选择“Web” > “ASP.NET Core Web 应用程序” > “AspNetCoreStorage”。 然后选择“确定”。

    ![Visual Studio“新建项目”对话框的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. 在“新建 ASP.NET Core Web 应用程序”对话框中，选择“.NET Core” > “ASP.NET Core 2.0” > “Web 应用程序(模型-视图-控制器)”。 然后选择“确定”。

    ![“新建 ASP.NET Core Web 应用程序”对话框的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>使用“连接服务”连接到 Azure 存储帐户

1. 在“解决方案资源管理器”中，右键单击项目。

2. 在上下文菜单中，依次选择“添加” > “连接服务”。

1. 在“连接服务”对话框中，选择“Azure 存储的云存储”，并选择“配置”。

    ![“连接服务”对话框的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. 在“Azure 存储”对话框中，选择要在本教程中使用的 Azure 存储帐户。 若要新建 Azure 存储帐户，请选择“新建存储帐户”并填写表单。 选择现有存储帐户或创建新的存储帐户后，选择“添加”。 Visual Studio 将会安装 Azure 存储的 NuGet 包，并将一个存储连接字符串添加到 **appsettings.json**。

> [!TIP]
> 若要了解如何使用 [Azure 门户](https://portal.azure.com)创建存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。
>
> 也可以使用 [Azure PowerShell](../storage/common/storage-powershell-guide-full.md)、[Azure CLI](../storage/common/storage-azure-cli.md) 或 [Azure Cloud Shell](../cloud-shell/overview.md) 创建存储帐户。


### <a name="create-an-mvc-controller"></a>创建 MVC 控制器 

1. 在**解决方案资源管理器**中，右键单击“控制器”。

2. 从上下文菜单中，选择“添加” > “控制器”。

    ![解决方案资源管理器的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. 在“添加基架”对话框中，选择“MVC 控制器 - 空”，并选择“添加”。

    ![“添加基架”对话框的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. 在“添加空 MVC 控制器”对话框中，将控制器命名为“BlobsController”，然后选择“添加”。

    ![“添加空 MVC 控制器”对话框的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. 将以下 `using` 指令添加到 `BlobsController.cs` 文件：

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>连接到存储帐户并获取容器引用

Blob 容器是由 Blob 和文件夹组成的嵌套式层次结构。 本文档中的余下步骤需要引用 Blob 容器，以便在其自身的方法中放置代码来实现可重用性。

以下步骤创建一个方法用于使用 **appsettings.json** 中的连接字符串连接到存储帐户。 这些步骤还创建对容器的引用。 **appsettings.json** 中的连接字符串设置使用 `<storageaccountname>_AzureStorageConnectionString` 格式进行命名。 

1. 打开 `BlobsController.cs` 文件。

1. 添加一个名为 **GetCloudBlobContainer**、可返回 **CloudBlobContainer** 的方法。 请务必将`<storageaccountname>_AzureStorageConnectionString` 替换为 **Web.config** 中的键的实际名称。
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
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

1. 调用 `CloudBlobContainer.CreateIfNotExists` 方法以创建容器（如果尚不存在）。 如果容器原本不存在，但已成功创建，则 `CloudBlobContainer.CreateIfNotExists` 方法返回 **true**。 否则，该方法返回 **false**。    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
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
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. 在**解决方案资源管理器**中，右键单击“视图”文件夹。

2. 从上下文菜单中，选择“添加” > “新建文件夹”。 将新文件夹命名为 *Blobs*。 

1. 在**解决方案资源管理器**中，展开“视图”文件夹，并右键单击 **Blob**。

4. 在上下文菜单中，选择“添加” > “视图”。

1. 在“添加视图”对话框中，输入 **CreateBlobContainer** 作为视图名称，并选择“添加”。

1. 打开 `CreateBlobContainer.cshtml`，并按以下代码片段所示对其进行修改：

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. 在“解决方案资源管理器”中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 查看未排序的列表，如下所示：`<ul class="nav navbar-nav">`。  在列表中最后一个 `<li>` 元素的后面，添加以下 HTML 中以添加另一个导航菜单项：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. 运行应用程序，并选择“创建 Blob 容器”，查看结果是否与以下屏幕截图类似：
  
    ![“创建 blob 容器”的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    如前所述，仅当容器原本不存在但已创建时，`CloudBlobContainer.CreateIfNotExists` 方法才返回 **true**。 因此，如果在容器存在的情况下运行该应用，该方法会返回 **false**。

## <a name="upload-a-blob-into-a-blob-container"></a>将 Blob 上传到 Blob 容器中

[创建 Blob 容器](#create-a-blob-container)之后，请将文件上传到该容器中。 本部分逐步讲解如何将本地文件上传到 Blob 容器。 相关步骤假设已有一个名为 *test-blob-container* 的 Blob 容器。 

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
        blob.UploadFromStreamAsync(fileStream).Wait();
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
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. 在“解决方案资源管理器”中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在列表中最后一个 `<li>` 元素的后面，添加以下 HTML 中以添加另一个导航菜单项：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. 运行该应用程序，并选择“上传 Blob”。 此时应显示文字“成功!” 。
    
    ![成功验证的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
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
   
1. 若要列出 Blob 容器中的 Blob，请使用 `CloudBlobContainer.ListBlobsSegmentedAsync` 方法。 `CloudBlobContainer.ListBlobsSegmentedAsync` 方法将返回 `BlobResultSegment`。 该项包含可强制转换为 `CloudBlockBlob`、`CloudPageBlob` 或 `CloudBlobDirectory` 对象的 `IListBlobItem` 对象。 以下代码片段枚举 blob 容器中的所有 blob。 每个 blob 均可基于其类型强制转换为相应的对象。 其名称或 URI（`CloudBlobDirectory` 的情况下）将添加到列表。

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    下面显示了已完成的 `ListBlobs` 方法：

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. 在**解决方案资源管理器**中，展开“视图”文件夹，并右键单击 **Blob**。

2. 在上下文菜单中，选择“添加” > “视图”。

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

1. 在“解决方案资源管理器”中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在列表中最后一个 `<li>` 元素的后面，添加以下 HTML 中以添加另一个导航菜单项：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. 运行应用程序，并选择“列出 blob”，查看结果是否与以下屏幕截图类似：
  
    ![“列出 blob”的屏幕截图](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>下载 Blob

本部分演示如何下载 blob。 可以将其保存到本地存储中或将其内容读取到一个字符串中。 示例代码引用在[创建 Blob 容器](#create-a-blob-container)部分创建的 test-blob-container。

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

1. 调用 `CloudBlobContainer.GetBlockBlobReference` 方法获取 Blob 引用对象。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. 若要下载 Blob，请使用 `CloudBlockBlob.DownloadToStream` 方法。 以下代码将 blob 的内容传输到流对象。 该对象随后会保存到本地文件中。 （将 *&lt;local-file-name>* 更改为完全限定文件名，表示要将 Blob 下载到的位置。） 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
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
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. 在“解决方案资源管理器”中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在列表中最后一个 `<li>` 元素的后面，添加以下 HTML 中以添加另一个导航菜单项：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. 运行该应用程序，并选择“下载 Blob”下载 Blob。 `CloudBlobContainer.GetBlockBlobReference` 方法调用中指定的 Blob 会将内容下载到 `File.OpenWrite` 方法调用中指定的位置。 浏览器中应会显示 文字“success!”。 

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

1. 调用 `CloudBlobContainer.GetBlockBlobReference` 方法获取 Blob 引用对象。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. 若要删除 Blob，请使用 `Delete` 方法。

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    已完成的 `DeleteBlob` 方法应如下所示：
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. 在“解决方案资源管理器”中，展开“视图” > “共享”文件夹，并打开 `_Layout.cshtml`。

1. 在列表中最后一个 `<li>` 元素的后面，添加以下 HTML 中以添加另一个导航菜单项：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. 运行该应用程序，并选择“删除 Blob”删除 `CloudBlobContainer.GetBlockBlobReference` 方法调用中指定的 Blob。 浏览器中应会显示 文字“success!”。 选择浏览器的“后退”按钮，并选择“列出 Blob”以验证该 Blob 是否不再在容器中。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用 ASP.NET Core 在 Azure 存储中存储、列出和检索 Blob。 查看更多功能指南，以了解在 Azure 中存储数据的其他方式。

  * [开始使用 Azure 表存储和 Visual Studio 连接服务 (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [开始使用 Azure 队列存储和 Visual Studio 连接服务 (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
