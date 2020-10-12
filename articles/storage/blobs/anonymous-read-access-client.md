---
title: 使用 .NET 以匿名方式访问公共容器和 blob
titleSuffix: Azure Storage
description: 使用用于 .NET 的 Azure 存储客户端库以匿名方式访问公共容器和 blob。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088810"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>使用 .NET 以匿名方式访问公共容器和 blob

Azure 存储支持容器和 blob 的可选公共读取访问权限。 客户端可以使用 Azure 存储客户端库以匿名方式访问公共容器和 blob，还可以使用支持数据访问 Azure 存储的其他工具和实用程序。

本文介绍如何从 .NET 访问公共容器或 blob。 有关在容器上配置匿名读取访问权限的信息，请参阅 [配置容器和 blob 的匿名公共读取访问权限](anonymous-read-access-configure.md)。 有关阻止对存储帐户进行匿名访问的详细信息，请参阅 [阻止对容器和 blob 的匿名公共读取访问](anonymous-read-access-prevent.md)。

如果某个客户端需要以匿名方式访问容器和 Blob，该客户端则可以使用不需要凭据的构造函数。 以下示例演示如何通过多种不同的方法以匿名方式引用容器和 Blob。

## <a name="create-an-anonymous-client-object"></a>创建匿名客户端对象

通过提供帐户的 Blob 存储终结点，可以创建一个可匿名访问的新服务客户端对象。 但是，也必须要知道该帐户中允许进行匿名访问的容器的名称。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>以匿名方式引用容器

如果拥有可以通过匿名方式使用的容器的 URL，则可使用该 URL 来直接引用容器。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>以匿名方式引用 Blob

如果拥有允许进行匿名访问的 Blob 的 URL，则可使用该 URL 来直接引用 Blob：

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>后续步骤

- [配置容器和 blob 的匿名公共读取访问权限](anonymous-read-access-configure.md)
- [阻止对容器和 blob 的匿名公共读取访问](anonymous-read-access-prevent.md)
- [授权访问 Azure 存储](../common/storage-auth.md)
