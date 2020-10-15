---
title: 使用 .NET 列出 Blob 容器 - Azure 存储
description: 了解如何使用 .NET 客户端库列出 Azure 存储帐户中的 Blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ab7749c93f39d0c7b630b63e0b0e68589b61ede2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090941"
---
# <a name="list-blob-containers-with-net"></a>使用 .NET 列出 Blob 容器

通过代码列出 Azure 存储帐户中的容器时，可以指定多个选项来管理如何从 Azure 存储返回结果。 本文介绍如何使用[适用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage)列出容器。  

## <a name="understand-container-listing-options"></a>了解容器列出选项

若要列出存储帐户中的容器，请调用以下方法之一：

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

这些方法的重载提供更多选项用于管理列出操作返回容器的方式。 后续部分将介绍这些选项。

### <a name="manage-how-many-results-are-returned"></a>管理要返回的结果数

默认情况下，列出操作每次最多返回 5000 个结果。 若要返回较小的结果集，请为要返回的结果页的大小提供一个非零值。

如果你的存储帐户包含5000个以上的容器，或者，如果你指定了一个页面大小，以使列表操作在存储帐户中返回容器的一个子集，则 Azure 存储将返回一个 *继续标记* ，其中包含容器的列表。 继续标记是一个不透明值，可用于从 Azure 存储中检索下一组结果。

在代码中检查继续标记的值，以确定它是否为适用于 .net v12) 的空 (或 .NET v11 和更早) 的 null (。 如果继续标记为 null，则表示结果集是完整的。 如果继续标记不为空，则再次调用该列表方法，并传入继续标记以检索下一组结果，直到继续标记为 null。

### <a name="filter-results-with-a-prefix"></a>使用前缀筛选结果

若要筛选容器列表，请为 `prefix` 参数指定一个字符串。 前缀字符串可以包含一个或多个字符。 然后，Azure 存储只返回其名称以该前缀开头的容器。

### <a name="return-metadata"></a>返回元数据

若要在结果中返回容器元数据，请为 .net v12) 指定[BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits)枚举 (的**元数据**值，并为 .net v11 和更早的) 指定[ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) (enum。 Azure 存储包括每个容器返回的元数据，因此你无需获取容器元数据。

## <a name="example-list-containers"></a>示例：列出容器

以下示例以异步方式列出存储帐户中以指定的前缀开头的容器。 该示例列出了以指定的前缀开头的容器，并为每次调用列表操作返回指定数目的结果。 然后，它使用继续标记获取下一段结果。 该示例还会连同结果一起返回容器元数据。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

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

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另请参阅

- [列出容器](/rest/api/storageservices/list-containers2)
- [枚举 Blob 资源](/rest/api/storageservices/enumerating-blob-resources)
