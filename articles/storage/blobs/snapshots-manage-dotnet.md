---
title: 在 .NET 中创建和管理 blob 快照
titleSuffix: Azure Storage
description: 了解如何在指定时刻及时创建 blob 的只读快照以备份 blob 数据。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 438692d5c142c3f617ee9d0c3f55b9b3740f9b7a
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884691"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>在 .NET 中创建和管理 blob 快照

快照是在某一时间点拍摄的只读版本的 Blob。 本文介绍如何使用[适用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)创建和管理 blob 快照。

有关 Azure 存储中 blob 快照的详细信息，请参阅[在 .net 中创建和管理 blob 快照](snapshots-overview.md)。

## <a name="create-a-snapshot"></a>创建快照

# <a name="net-version-12x"></a>[.NET 版本2。x](#tab/v12)

若要使用适用于 .NET 的 Azure 存储客户端库的版本2.x 创建块 blob 的快照，请使用以下方法之一：

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

下面的代码示例演示如何创建版本为版本为的快照。 包括对[Azure. 标识](https://www.nuget.org/packages/azure.identity)库的引用，以使用 Azure AD 凭据向服务请求授权。

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-version-11x"></a>[.NET 版本 11. x](#tab/v11)

若要使用适用于 .NET 的 Azure 存储客户端库的版本 11. x 创建块 blob 的快照，请使用以下方法之一：

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

下面的代码示例演示如何创建版本为版本 11. x 的快照。 本示例在创建快照时为其指定了其他元数据。

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
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

## <a name="delete-snapshots"></a>删除快照

若要删除 blob，必须先删除该 blob 的所有快照。 可以单独删除快照，或指定在删除源 Blob 时删除所有快照。 如果尝试删除仍包含快照的 Blob，会发生错误。

# <a name="net-version-12x"></a>[.NET 版本2。x](#tab/v12)

若要使用适用于 .NET 的 Azure 存储客户端库的版本2.x 删除 blob 及其快照，请使用以下方法之一，并包含[DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption)枚举：

- [删除](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

下面的代码示例演示如何在 .NET 中删除 blob 及其快照，其中`blobClient`是[BlobClient](/dotnet/api/azure.storage.blobs.blobclient)类型的对象）：

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-version-11x"></a>[.NET 版本 11. x](#tab/v11)

若要使用适用于 .NET 的 Azure 存储客户端库的版本4.x 删除 blob 及其快照，请使用以下 blob 删除方法之一，并包含[DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)枚举：

- [删除](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

下面的代码示例演示如何在 .NET 中删除 blob 及其快照，其中`blockBlob`是类型为 [CloudBlockBlob] [dotnet_CloudBlockBlob] 的对象：

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>后续步骤

- [Blob 快照](snapshots-overview.md)
- [Blob 版本（预览）](versioning-overview.md)
- [Blob 的软删除](storage-blob-soft-delete.md)