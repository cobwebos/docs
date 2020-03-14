---
title: 使用 .NET 复制 blob-Azure 存储
description: 了解如何使用 .NET 客户端库复制 Azure 存储帐户中的 blob。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135881"
---
# <a name="copy-a-blob-with-net"></a>使用 .NET 复制 blob

本文演示如何将 blob 复制到 Azure 存储帐户。 它还演示了如何中止异步复制操作。 示例代码使用适用于[.net 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)。

## <a name="about-copying-blobs"></a>关于复制 blob

在同一存储帐户内复制 blob 时，它是同步操作。 跨帐户复制时，它是一个异步操作。 [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)和[StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)方法返回一个复制 ID 值，该值用于检查状态或中止复制操作。

复制操作的源 blob 可以是块 blob、追加 blob、页 blob 或快照。 如果目标 Blob 已存在，它的 Blob 类型必须与源 Blob 相同。 将覆盖任何现有目标 Blob。 

当复制操作正在进行时，无法修改目标 blob。 目标 Blob 只能有一个未完成的复制 Blob 操作。 换句话说，blob 不能作为多个挂起的复制操作的目标。

始终复制整个源 blob 或文件。 不支持复制一系列字节或一组块。

复制 blob 时，会将系统属性复制到具有相同值的目标 blob。

对于所有 blob 类型，可以检查目标 blob 上的[CopyState](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet)属性，以获取复制操作的状态。 复制完成时，将提交最终的 Blob。

复制操作可以采用以下任何形式：

  - 可以将源 Blob 复制到名称不同的目标 Blob。 目标 blob 可以是相同 blob 类型（块、追加或页）的现有 blob，也可以是复制操作创建的新 blob。
  - 可以将源 blob 复制到具有相同名称的目标 blob，从而有效地替换目标 blob。 此类复制操作将删除所有未提交的块，并覆盖目标 blob 的元数据。
  - 可以将 Azure 文件服务中的源文件复制到目标 blob。 目标 blob 可以是现有块 blob，也可以是复制操作创建的新块 blob。 不支持从文件复制到页 blob 或追加 blob。
  - 可以将快照复制到其基本 Blob 上。 通过将快照提升到基本 Blob 的位置，可还原早期版本的 Blob。
  - 可将快照复制到具有不同名称的目标 Blob。 生成的目标 Blob 是可编写的 Blob，而不是快照。

## <a name="copy-a-blob"></a>复制 blob

若要复制 blob，请调用以下方法之一：

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

下面的代码示例获取对先前创建的 blob 的引用，并将其复制到同一容器中的新 blob：

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>中止 blob 复制操作

中止复制操作会生成块 blob、追加 blob 和页 blob 的长度为零的目标 blob。 但是，目标 blob 的元数据将包含从源 blob 中复制的新值，或在[StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)或[StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)调用中显式设置的值。 若要在复制之前保留原始元数据，请在调用 `StartCopy` 或 `StartCopyAsync`之前创建目标 blob 的快照。

中止正在进行的 blob 复制操作时，目标 blob 的[CopyState](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status)设置为[CopyStatus](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet)。

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet)和[AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet)方法取消正在进行的 blob 复制操作，并保留长度为零的目标 blob 和完整元数据。

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>后续步骤

以下主题包含有关使用 Azure REST Api 复制 blob 和中止正在进行的复制操作的信息。

- [复制 Blob](/rest/api/storageservices/copy-blob)
- [中止复制 Blob](/rest/api/storageservices/abort-copy-blob)
