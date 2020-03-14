---
title: 列出 blob 容器与 .NET-Azure 存储
description: 了解如何使用 .NET 客户端库列出 Azure 存储帐户中的 blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135898"
---
# <a name="list-blob-containers-with-net"></a>用 .NET 列出 blob 容器

当你从代码列出 Azure 存储帐户中的容器时，可以指定多个选项来管理如何从 Azure 存储中返回结果。 本文介绍如何使用[用于 .net 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)列出容器。  

## <a name="understand-container-listing-options"></a>了解容器列表选项

若要列出存储帐户中的容器，请调用以下方法之一：

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

这些方法的重载提供其他选项，用于管理列表操作返回容器的方式。 以下部分介绍了这些选项。

### <a name="manage-how-many-results-are-returned"></a>管理返回的结果数

默认情况下，列表操作一次最多返回5000个结果。 若要返回较小的结果集，请在调用**ListContainerSegmented**方法之一时为 `maxresults` 参数提供一个非零值。

如果你的存储帐户包含5000个以上的容器，或者，如果你已为 `maxresults` 指定了一个值，这样，列表操作将返回存储帐户中的容器的子集，则 Azure 存储将返回一个*继续标记*，其中包含容器的列表。 继续标记是可用于从 Azure 存储中检索下一组结果的不透明值。

在代码中检查继续标记的值，以确定它是否为 null。 当继续标记为 null 时，结果集将完成。 如果继续标记不为 null，则再次调用**ListContainersSegmented**或**ListContainersSegmentedAsync** ，并传入继续标记以检索下一组结果，直到继续标记为 null。

### <a name="filter-results-with-a-prefix"></a>使用前缀筛选结果

若要筛选容器列表，请为 `prefix` 参数指定一个字符串。 前缀字符串可以包含一个或多个字符。 然后，Azure 存储只返回其名称以该前缀开头的容器。

### <a name="return-metadata"></a>返回元数据

若要返回包含结果的容器元数据，请指定[ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails)枚举的**元数据**值。 Azure 存储包括每个返回的容器的元数据，因此你无需调用**FetchAttributes**方法之一来检索容器元数据。

## <a name="example-list-containers"></a>示例：列出容器

下面的示例以异步方式列出了以指定前缀开头的存储帐户中的容器。 该示例一次列出容器以5个结果为增量，并使用继续标记获取下一段结果。 该示例还返回容器元数据和结果。

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
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

## <a name="see-also"></a>另请参阅

[列出容器](/rest/api/storageservices/list-containers2)
[枚举 Blob 资源](/rest/api/storageservices/enumerating-blob-resources)
