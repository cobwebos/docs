---
title: 列出 blob 和 .NET-Azure 存储
description: 了解如何使用 .NET 客户端库列出 Azure 存储帐户的容器中的 blob。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/04/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: bf9d2d59e993de3807a10a6c39f88b2063024bfc
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599924"
---
# <a name="list-blobs-with-net"></a>用 .NET 列出 blob

列出代码中的 blob 时，可以指定多个选项来管理从 Azure 存储返回结果的方式。 本文介绍如何使用[用于 .net 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage/client)列出 blob。  

## <a name="understand-blob-listing-options"></a>了解 blob 列表选项

若要列出存储帐户中的 blob，请调用以下方法之一：

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

若要列出容器中的 blob，请调用以下方法之一：

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

这些方法的重载提供其他选项，用于管理列表操作如何返回 blob。 以下部分介绍了这些选项。

### <a name="manage-how-many-results-are-returned"></a>管理返回的结果数

默认情况下，列表操作一次最多返回5000个结果。 若要返回较小的结果集，请在调用**ListBlobs**方法之一时为 `maxresults` 参数提供一个非零值。

如果列表操作返回5000个以上的 blob，或者如果已为 `maxresults` 指定一个值，这样，列表操作将返回存储帐户中的容器的子集，则 Azure 存储将返回包含 blob 列表的*继续标记*。 继续标记是可用于从 Azure 存储中检索下一组结果的不透明值。

在代码中检查继续标记的值，以确定它是否为 null。 当继续标记为 null 时，结果集将完成。 如果继续标记不为 null，则再次调用列出操作，并传入继续标记以检索下一组结果，直到继续标记为 null。

### <a name="filter-results-with-a-prefix"></a>使用前缀筛选结果

若要筛选容器列表，请为 `prefix` 参数指定一个字符串。 前缀字符串可以包含一个或多个字符。 然后，Azure 存储只返回其名称以该前缀开头的 blob。

### <a name="return-metadata"></a>返回元数据

若要在结果中返回 blob 元数据，请指定[BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails)枚举的**元数据**值。 Azure 存储包括每个返回的 blob 的元数据，因此无需在此上下文中调用**FetchAttributes**方法之一来检索 blob 元数据。

### <a name="flat-listing-versus-hierarchical-listing"></a>简单列表与分层列表

Azure 存储中的 blob 按平面模式而不是分层范例（如经典文件系统）组织。 不过，您可以将 blob 组织到*虚拟目录*中，以模拟分层范例。 虚拟目录是 demarcated 的名称的一部分，该 blob 由分隔符字符进行。

若要将 blob 组织为虚拟目录，请在 blob 名称中使用分隔符字符。 默认分隔符是一个正斜杠（/），但您可以指定任何字符作为分隔符。

如果使用分隔符命名 blob，则可以选择按层次结构列出 blob。 对于分层列表操作，Azure 存储返回父对象下的所有虚拟目录和 blob。 您可以以递归方式调用列表操作来遍历层次结构，类似于以编程方式遍历经典文件系统。

## <a name="use-a-flat-listing"></a>使用简单列表

默认情况下，列表操作以平面列表方式返回 blob。 在平面列表中，blob 不按虚拟目录进行组织。

下面的示例使用平面列表（指定了可选的段大小）列出指定容器中的 blob，并将 blob 名称写入到控制台窗口。

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

示例输出类似于：

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>使用分层列表

在层次结构中调用列表操作时，Azure 存储将返回层次结构第一级的虚拟目录和 blob。 设置每个虚拟目录的[prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix)属性，以便可以在递归调用中传递前缀以检索下一个目录。

若要以分层方式列出 blob，请将列表方法的 `useFlatBlobListing` 参数设置为**false**。

下面的示例使用平面列表（指定了可选的段大小）列出指定容器中的 blob，并将 blob 名称写入控制台窗口。

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and 
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, null, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

示例输出类似于：

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> 在分层列表操作中不能列出 Blob 快照。

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>后续步骤

- [列出 Blob](/rest/api/storageservices/list-blobs)
- [枚举 Blob 资源](/rest/api/storageservices/enumerating-blob-resources)
