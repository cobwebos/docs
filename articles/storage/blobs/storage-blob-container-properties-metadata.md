---
title: 使用 .NET 管理 blob 容器的属性和元数据
titleSuffix: Azure Storage
description: 了解如何使用 .NET 客户端库设置和检索系统属性，以及在 Azure 存储帐户中的 blob 容器上存储自定义元数据。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135915"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>通过 .NET 管理容器属性和元数据

Blob 容器除了支持系统属性和用户定义的元数据之外，还支持系统属性和用户定义元数据。 本文介绍如何通过[用于 .net 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)管理系统属性和用户定义的元数据。

## <a name="about-properties-and-metadata"></a>关于属性和元数据

- **系统属性**：系统属性存在于每个 Blob 存储资源上。 其中一些属性是可以读取或设置的，而另一些属性是只读的。 事实上，有些系统属性与某些标准 HTTP 标头对应。 适用于 .NET 的 Azure 存储客户端库维护这些属性。

- **用户定义的元数据**：用户定义的元数据由你为 Blob 存储资源指定的一个或多个名称/值对组成。 您可以使用元数据来存储资源的其他值。 元数据值仅用于你自己的目的，不会影响资源的行为方式。

检索 Blob 存储资源的属性和元数据值的过程分为两个步骤。 必须先通过调用 FetchAttributes 或 FetchAttributesAsync 方法显式提取这些值，然后才能读取。 此规则的例外情况是， **Exists**和**ExistsAsync**方法会调用所涵盖的相应**FetchAttributes**方法。 调用这些方法之一时，无需调用**FetchAttributes**。

> [!IMPORTANT]
> 如果发现尚未填充存储资源的属性或元数据值，请检查代码是否调用了 FetchAttributes 或 FetchAttributesAsync 方法。

元数据名称/值对是有效的 HTTP 标头，因此应遵循控制 HTTP 标头的所有限制。 元数据名称必须是有效的 HTTP 标头C#名称和有效标识符，只能包含 ASCII 字符，并且应视为不区分大小写。 包含非 ASCII 字符的元数据值应采用 Base64 编码或 URL 编码。

## <a name="retrieve-container-properties"></a>检索容器属性

若要检索容器属性，请调用以下方法之一：

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

下面的代码示例获取容器的系统属性，并将一些属性值写入到控制台窗口：

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

可以在 Blob 或容器资源上指定元数据作为一个或多个名称/值对。 若要设置元数据，请将名称/值对添加到资源上的**元数据**集合中，然后调用以下方法之一来写入值：

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

元数据的名称必须符合 C# 标识符命名约定。 元数据名称保留创建它们的大小写，但在设置或读取时不区分大小写。 如果为资源提交了两个或多个具有相同名称的元数据标头，Blob 存储将返回 HTTP 错误代码400（错误请求）。

以下代码示例在容器上设置元数据。 一个值是使用集合的 **Add** 方法设置的。 另一个值是使用隐式键/值语法设置的。 这两种方法都有效。

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

要检索元数据，请对 blob 或容器调用 FetchAttributes 或 FetchAttributesAsync 方法以填充 Metadata 集合，然后读取值，如下面的示例所示。

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [获取容器属性操作](/rest/api/storageservices/get-container-properties)
- [设置容器元数据操作](/rest/api/storageservices/set-container-metadata)
- [获取容器元数据操作](/rest/api/storageservices/set-container-metadata)
