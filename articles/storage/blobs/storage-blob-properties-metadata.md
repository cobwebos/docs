---
title: 使用 .NET 管理 blob 的属性和元数据 - Azure 存储
description: 了解如何使用 .NET 客户端库设置和检索系统属性并将自定义元数据存储在 Azure 存储帐户的 Blob 中。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 74c023c06e7b28183a53772be6798419c91dd37a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692452"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>使用 .NET 管理 blob 属性和元数据

除 Blob 包含的数据外，它们还支持系统属性和用户定义的元数据。 本文介绍如何使用[用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)管理系统属性和用户定义元数据。

## <a name="about-properties-and-metadata"></a>关于属性和元数据

- **系统属性**：系统属性存在于每个 Blob 存储资源上。 其中一些属性是可以读取或设置的，而另一些属性是只读的。 事实上，有些系统属性与某些标准 HTTP 标头对应。 用于 .NET 的 Azure 存储客户端库将维护这些属性。

- **用户定义的元数据**：用户定义元数据包含一个或多个你为 Blob 存储资源指定的名称/值对对。 可以使用元数据存储资源的其他值。 元数据值仅用于你自己的目的，不会影响资源的行为方式。

> [!NOTE]
> 使用 blob 索引标记，还可以将任意用户定义的键/值属性与 Blob 存储资源一起存储。 与元数据类似，只会自动为 Blob 索引标记编制索引，并使其成为本机 Blob 服务的可查询。 除非使用了单独的服务（如 Azure 搜索），否则无法对元数据进行本机索引和查询。
>
> 若要了解有关此功能的详细信息，请参阅[在 Azure Blob 存储中管理和查找数据（预览版）](storage-manage-find-blobs.md)。

检索 Blob 存储资源的元数据和属性值的过程分为两步。 必须先调用 `FetchAttributes` 或 `FetchAttributesAsync` 方法显式获取这些值，然后才能读取它们。 此规则的例外是，`Exists` 和 `ExistsAsync` 方法在后台调用相应的 `FetchAttributes` 方法。 调用这其中的一个方法时，不需同时调用 `FetchAttributes`。

> [!IMPORTANT]
> 如果发现尚未填充存储资源的属性或元数据值，请检查代码是否调用了 `FetchAttributes` 或 `FetchAttributesAsync` 方法。

## <a name="set-and-retrieve-properties"></a>设置和检索属性

以下代码示例设置 blob 的 `ContentType` 和 `ContentLanguage` 系统属性。

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

若要检索 blob 属性，请对 blob 调用 `FetchAttributes` 或 `FetchAttributesAsync` 方法以填充 `Properties` 属性。 以下代码示例获取 blob 的系统属性并显示一些值：

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>设置和检索元数据

可以在 Blob 或容器资源上指定元数据作为一个或多个名称/值对。 若要设置元数据，请将名称/值对添加到资源上的 `Metadata` 集合。 然后，调用以下方法之一来写入值：

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

元数据名称/值对是有效的 HTTP 标头，因此应当遵循所有控制 HTTP 标头的限制。 元数据名称必须是有效的 HTTP 标头名称和有效的 C# 标识符，只能包含 ASCII 字符，并且应当区分大小写。 包含非 ASCII 字符的 [Base64 编码](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string)或 [URL 编码](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode)的元数据值。

元数据的名称必须符合 C# 标识符命名约定。 元数据名称保留创建时使用的大小写，但在设置或读取时不区分大小写。 如果为资源提交了两个或更多个名称相同的元数据标头，Azure Blob 存储会返回 HTTP 错误代码“400 (请求错误)”。

以下代码示例在 blob 上设置元数据。 一个值是使用集合的 `Add` 方法设置的。 另一个值是使用隐式键/值语法设置的。

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

若要检索元数据，请对 blob 或容器调用 `FetchAttributes` 或 `FetchAttributesAsync` 方法以填充 `Metadata` 集合，并读取值，如下面的示例所示。

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另请参阅

- [设置 Blob 属性操作](/rest/api/storageservices/set-blob-properties)
- [获取 Blob 属性操作](/rest/api/storageservices/get-blob-properties)
- [设置 Blob 元数据操作](/rest/api/storageservices/set-blob-metadata)
- [获取 Blob 元数据操作](/rest/api/storageservices/get-blob-metadata)
