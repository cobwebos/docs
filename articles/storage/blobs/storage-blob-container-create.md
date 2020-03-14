---
title: 使用 .NET 创建或删除 blob 容器-Azure 存储
description: 了解如何使用 .NET 客户端库创建或删除 Azure 存储帐户中的 blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135932"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>使用 .NET 在 Azure 存储中创建或删除容器

Azure 存储中的 blob 被组织到容器中。 必须先创建容器，然后才能上传 blob。 本文介绍如何通过[用于 .net 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)创建和删除容器。

## <a name="name-a-container"></a>命名容器

容器名称必须是有效的 DNS 名称，因为它构成用于对容器或其 blob 进行寻址的唯一 URI 的一部分。 为容器命名时，请遵循以下规则：

- 容器名称长度可以在3到63个字符之间。
- 容器名称必须以字母或数字开头，并且只能包含小写字母、数字和短划线（-）字符。
- 容器名称中不允许出现两个或更多的连续短划线字符。

容器的 URI 采用以下格式：

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>创建容器

若要创建容器，请调用以下方法之一：

- [创建](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

如果已存在具有相同名称的容器， **Create**和**CreateAsync**方法将引发异常。

**CreateIfNotExists**和**CreateIfNotExistsAsync**方法返回一个布尔值，指示是否已创建容器。 如果已存在具有相同名称的容器，则这些方法将返回**False**以指示未创建新的容器。

紧接在存储帐户下创建容器。 不能在一个容器下嵌套另一个容器。

下面的示例将异步创建容器：

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>创建根容器

根容器将用作存储帐户的默认容器。 每个存储帐户可以有一个根容器，该容器必须命名 *$root。* 必须显式创建或删除根容器。

可以引用存储在根容器中的 blob，而不包括根容器名称。 根容器使你能够在存储帐户层次结构的顶层引用 blob。 例如，你可以通过以下方式引用驻留在根容器中的 Blob：

`https://myaccount.blob.core.windows.net/default.html`

以下示例同步创建根容器：

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>删除容器

若要在 .NET 中删除容器，请使用以下方法之一：

- [删除](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [Cloudblobcontainer.deleteifexistsasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

如果容器不存在， **Delete**和**DeleteAsync**方法将引发异常。

**DeleteIfExists**和**cloudblobcontainer.deleteifexistsasync**方法返回一个布尔值，指示是否已删除容器。 如果指定的容器不存在，则这些方法将返回**False**以指示未删除容器。

删除容器后，无法创建名称至少为30秒且可能更长的容器。 删除容器时，尝试创建具有相同名称的容器将失败，并出现 HTTP 错误代码409（冲突）。 删除容器时，容器或其包含的 blob 上的任何其他操作都将失败，并出现 HTTP 错误代码404（未找到）。

下面的示例将删除指定的容器，并在该容器不存在时处理异常：

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

下面的示例演示如何删除以指定的前缀开头的所有容器。 如果容器上存在租约，则此示例将中断租约。

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另请参阅

- [创建容器操作](/rest/api/storageservices/create-container)
- [Delete Container 操作](/rest/api/storageservices/delete-container)
