---
title: "从 Azure 存储下载大量随机数据 | Microsoft Docs"
description: "了解如何使用 Azure SDK 从 Azure 存储帐户下载大量随机数据"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 46b0cf3666088175372b6a2e73b3dd421a4bff8b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>从 Azure 存储下载大量随机数据

本教程是一个系列中的第三部分。 本教程介绍如何从 Azure 存储下载大量数据。

在该系列的第三部分中，你会学习如何：

> [!div class="checklist"]
> * 更新应用程序
> * 运行应用程序
> * 验证连接数

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须先完成上一存储教程：[将大量随机数据并行上传到 Azure 存储][previous-tutorial]。

## <a name="remote-into-your-virtual-machine"></a>远程登录到虚拟机

 若要创建与虚拟机的远程桌面会话，请在本地计算机上使用以下命令。 将 IP 地址替换为虚拟机的 publicIPAddress。 出现提示时，输入创建虚拟机时使用的凭据。

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>更新应用程序

上一教程中只将文件上传到了存储帐户。 在文本编辑器中打开 `D:\git\storage-dotnet-perf-scale-app\Program.cs`。 将 `Main` 方法替换为以下示例。 本示例添加了上传任务注释，取消了下载任务注释以及在完成时删除存储帐户中的内容这一任务的注释。

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initialy
        // As the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

应用程序更新后，需再次生成应用程序。 打开 `Command Prompt` 并导航到 `D:\git\storage-dotnet-perf-scale-app`。 通过运行 `dotnet build` 重新生成应用程序，如以下示例所示：

```
dotnet build
```

## <a name="run-the-application"></a>运行应用程序

现在，已重新生成应用程序，可使用更新的代码运行该应用程序。 如果尚未打开，请打开 `Command Prompt` 并导航到 `D:\git\storage-dotnet-perf-scale-app`。

键入 `dotnet run` 运行应用程序。

```
dotnet run
```

应用程序读取位于 storageconnectionstring 中指定的存储帐户中的容器。 它使用容器中的 [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) 方法每次循环访问 10 个 blob，并使用 [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) 方法将它们下载到本地计算机。
下表显示了每个 blob 下载完成后为其定义的 [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet)。

|属性|值|说明|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| 是| 该属性禁用对上传内容的 MD5 哈希检查。 禁用 MD5 验证可加快传输速度。 但是不能确认传输文件的有效性或完整性。 |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| 该属性确定是否计算和存储 MD5 哈希。   |

下例显示了 `DownloadFilesAsync` 任务：

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestionOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>验证连接

在下载文件的同时，可以验证存储帐户的并发连接数。 打开 `Command Prompt` 并键入 `netstat -a | find /c "blob:https"`。 此命令显示当前使用 `netstat` 打开的连接数。 下例显示的输出与自己运行该教程时看到的输出类似。 如该示例所示，从存储帐户下载随机文件时，打开了 280 多个连接。

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>后续步骤

本系列的第三部分介绍了从存储帐户下载大量随机数据的方法，例如如何：

> [!div class="checklist"]
> * 运行应用程序
> * 验证连接数

接下来进入本系列的第四部分，验证门户中的吞吐量和延迟指标。

> [!div class="nextstepaction"]
> [验证门户中的吞吐量和延迟指标](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md