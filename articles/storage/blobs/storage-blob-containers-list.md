---
title: 使用 .NET 列出 Blob 容器 - Azure 存储
description: 了解如何使用 .NET 客户端库列出 Azure 存储帐户中的 Blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a76b83218a194c2b5cbf3ce582e8094014004123
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803384"
---
# <a name="list-blob-containers-with-net"></a>使用 .NET 列出 Blob 容器

通过代码列出 Azure 存储帐户中的容器时，可以指定多个选项来管理如何从 Azure 存储返回结果。 本文介绍如何使用[适用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage/client)列出容器。  

## <a name="understand-container-listing-options"></a>了解容器列出选项

若要列出存储帐户中的容器，请调用以下方法之一：

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

这些方法的重载提供更多选项用于管理列出操作返回容器的方式。 后续部分将介绍这些选项。

### <a name="manage-how-many-results-are-returned"></a>管理要返回的结果数

默认情况下，列出操作每次最多返回 5000 条结果。 若要返回更少的结果，请在调用某个 **ListContainerSegmented** 方法时为 `maxresults` 参数提供非零值。

如果存储帐户包含 5000 个以上的容器，或者你为 `maxresults` 指定了一个导致列出操作返回存储帐户中一部分容器的值，则 Azure 存储将返回一个包含容器列表的继续标记。 继续标记是一个不透明值，可用于从 Azure 存储中检索下一组结果。

在代码中检查继续标记的值，以确定它是否为 null。 如果继续标记为 null，则表示结果集是完整的。 如果继续标记不为 null，则再次调用 **ListContainersSegmented** 或 **ListContainersSegmentedAsync**，并传入继续标记以检索下一组结果，直到继续标记为 null。

### <a name="filter-results-with-a-prefix"></a>使用前缀筛选结果

若要筛选容器列表，请为 `prefix` 参数指定一个字符串。 前缀字符串可以包含一个或多个字符。 然后，Azure 存储只返回其名称以该前缀开头的容器。

### <a name="return-container-metadata"></a>返回容器元数据

若要连同结果一起返回容器元数据，请指定 [ContainerListDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) 枚举的 **Metadata** 值。 Azure 存储包含每个返回的容器的元数据，因此无需同时调用 **FetchAttributes** 方法之一即可检索容器元数据。

## <a name="example-list-containers"></a>例如：列出容器

以下示例以异步方式列出存储帐户中以指定的前缀开头的容器。 该示例每次以 5 个结果为增量列出容器，并使用继续标记获取下一个结果段。 该示例还会连同结果一起返回容器元数据。

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>请参阅

[列出容器](/rest/api/storageservices/list-containers2)
[枚举 Blob 资源](/rest/api/storageservices/enumerating-blob-resources)
