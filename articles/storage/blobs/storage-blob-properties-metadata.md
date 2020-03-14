---
title: 使用 .NET-Azure 存储管理 blob 的属性和元数据
description: 了解如何使用 .NET 客户端库设置和检索系统属性，以及如何在 Azure 存储帐户中的 blob 上存储自定义元数据。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137656"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>通过 .NET 管理 blob 属性和元数据

除了它们包含的数据外，blob 还支持系统属性和用户定义元数据。 本文介绍如何通过[用于 .net 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)管理系统属性和用户定义的元数据。

## <a name="about-properties-and-metadata"></a>关于属性和元数据

- **系统属性**：系统属性存在于每个 Blob 存储资源上。 其中一些属性是可以读取或设置的，而另一些属性是只读的。 事实上，有些系统属性与某些标准 HTTP 标头对应。 适用于 .NET 的 Azure 存储客户端库维护这些属性。

- **用户定义的元数据**：用户定义的元数据由你为 Blob 存储资源指定的一个或多个名称/值对组成。 您可以使用元数据来存储资源的其他值。 元数据值仅用于自己的目的，不会影响资源的行为方式。

检索 Blob 存储资源的元数据和属性值的过程分为两个步骤。 你必须通过调用 `FetchAttributes` 或 `FetchAttributesAsync` 方法显式获取这些值，然后才能读取这些值。 此规则的例外情况是，`Exists` 和 `ExistsAsync` 方法会调用所涉及的相应 `FetchAttributes` 方法。 调用这些方法之一时，无需调用 `FetchAttributes`。

> [!IMPORTANT]
> 如果发现存储资源的属性或元数据值尚未填充，请检查代码是否调用了 `FetchAttributes` 或 `FetchAttributesAsync` 方法。

## <a name="set-and-retrieve-properties"></a>设置和检索属性

下面的代码示例设置 blob 上的 `ContentType` 和 `ContentLanguage` 系统属性。

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

若要检索 blob 属性，请对 blob 调用 `FetchAttributes` 或 `FetchAttributesAsync` 方法来填充 `Properties` 属性。 下面的代码示例获取 blob 的系统属性，并显示一些值：

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

可以在 Blob 或容器资源上指定元数据作为一个或多个名称/值对。 若要设置元数据，请将名称/值对添加到资源上的 `Metadata` 集合。 然后，调用下列方法之一来写入值：

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

元数据名称/值对是有效的 HTTP 标头，应遵循控制 HTTP 标头的所有限制。 元数据名称必须是有效的 HTTP 标头C#名称和有效标识符，只能包含 ASCII 字符，并且应视为不区分大小写。 [Base64 编码](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string)或[URL 编码](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode)包含非 ASCII 字符的元数据值。

元数据的名称必须符合 C# 标识符命名约定。 元数据名称保持创建时使用的大小写，但在设置或读取时不区分大小写。 如果为资源提交了使用同一名称的两个或多个元数据标头，Azure Blob 存储将返回 HTTP 错误代码400（错误请求）。

下面的代码示例在 blob 上设置元数据。 使用集合的 `Add` 方法设置一个值。 另一个值是使用隐式键/值语法设置的。

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

若要检索元数据，请对 blob 或容器调用 `FetchAttributes` 或 `FetchAttributesAsync` 方法来填充 `Metadata` 集合，然后读取值，如下面的示例中所示。

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
