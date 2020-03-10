---
title: 管理容器和 blob 的公共读取访问权限
titleSuffix: Azure Storage
description: 了解如何使容器和 blob 可供匿名访问，以及如何对其进行程序式访问。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360910"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>管理对容器和 blob 的匿名读取访问

可以启用对 Azure Blob 存储中的容器及其 Blob 的匿名公共读取访问。 这样做可以授予对这些资源的只读访问权限，无需共享帐户密钥，也无需共享访问签名 (SAS)。

如果想要始终允许对某些 Blob 进行匿名读取访问，最好的方法是启用公共读取访问。 可以创建共享访问签名，实现更精细的控制。 利用共享访问签名，可以针对特定时间段提供使用不同权限的受限访问。 有关创建共享访问签名的详细信息，请参阅[在 Azure 存储中使用共享访问签名 (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>授予对容器和 blob 的匿名用户权限

默认情况下，容器和其中的任何 blob 只能由已被授予适当权限的用户访问。 若要授予匿名用户对容器及其 blob 的读取权限，可以设置容器公共访问级别。 授予对容器的公共访问权限时，匿名用户可以读取可公开访问的容器中的 blob，而无需对请求进行授权。

可为容器配置以下权限：

- 非公共读取访问：只有存储帐户所有者可以访问容器及其 Blob。 这是所有新容器的默认权限。
- 仅针对 Blob 的公共读取访问：可通过匿名请求读取容器中的 Blob，但容器数据不可用。 匿名客户端无法枚举容器中的 Blob。
- **容器及其 blob 的公共读取访问权限：** 所有容器和 blob 数据可通过匿名请求读取。 客户端可以通过匿名请求枚举容器中的 Blob，但无法枚举存储帐户中的容器。

### <a name="set-container-public-access-level-in-the-azure-portal"></a>在 Azure 门户中设置容器公共访问级别

在[Azure 门户](https://portal.azure.com)中，可以更新一个或多个容器的公共访问级别：

1. 在 Azure 门户中导航到存储帐户概述。
1. 在菜单边栏选项卡上的 " **blob 服务**" 下，选择 " **blob**"。
1. 选择要为其设置公共访问级别的容器。
1. 使用 "**更改访问级别**" 按钮显示公共访问设置。
1. 从 "**公共访问级别**" 下拉列表中选择所需的公共访问级别，然后单击 "确定" 按钮，将更改应用到所选容器。

以下屏幕截图显示了如何更改所选容器的公共访问级别。

![显示如何在门户中设置公共访问级别的屏幕截图](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> 不能更改单个 blob 的公共访问级别。 公共访问级别仅在容器级别设置。

### <a name="set-container-public-access-level-with-net"></a>使用 .NET 设置容器公共访问级别

若要使用适用于 .NET 的 Azure 存储客户端库设置容器的权限，请先通过调用以下方法之一来检索容器的现有权限：

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

接下来，设置**GetPermissions**方法返回的[BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions)对象的**PublicAccess**属性。

最后，调用以下方法之一更新容器的权限：

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

以下示例将容器的权限设置为完全公共读取访问。 要将权限设置为仅针对 blob 的公共读取访问，请将 **PublicAccess** 属性设置为 **BlobContainerPublicAccessType.Blob**。 要删除匿名用户的所有权限，请将该属性设置为 **BlobContainerPublicAccessType.Off**。

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>匿名访问容器和 blob

如果某个客户端需要以匿名方式访问容器和 blob，则可以让该客户端使用不需要凭据的构造函数。 下面的示例演示了以匿名方式引用容器和 blob 的几种不同方式。

### <a name="create-an-anonymous-client-object"></a>创建匿名客户端对象

可以通过提供帐户的 Blob 存储终结点，为匿名访问创建新的服务客户端对象。 不过，你还必须知道该帐户中允许进行匿名访问的容器的名称。

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

### <a name="reference-a-container-anonymously"></a>以匿名方式引用容器

如果你有容器的 URL，而该容器可以通过匿名方式来使用，则可使用该 URL 来直接引用容器。

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

### <a name="reference-a-blob-anonymously"></a>以匿名方式引用 blob

如果你有 blob 的 URL，而该 blob 允许进行匿名访问，则可使用该 URL 来直接引用 blob：

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>后续步骤

- [授权访问 Azure 存储](../common/storage-auth.md)
- [使用共享访问签名（SAS）授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)
- [Blob 服务 REST API](/rest/api/storageservices/blob-service-rest-api)
