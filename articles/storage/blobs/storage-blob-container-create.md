---
title: 使用 .NET 创建或删除 Blob 容器 - Azure 存储
description: 了解如何使用 .NET 客户端库在 Azure 存储帐户中创建或删除 Blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: d1218b10eadf0788752bab2aec4b21614666888c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671283"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>使用 .NET 在 Azure 存储中创建或删除容器

Azure 存储中的 Blob 已组织成容器。 必须先创建容器，才能上传 Blob。 本文介绍如何使用[适用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage/client)创建和删除容器。

## <a name="name-a-container"></a>为容器命名

容器名称必须是有效的 DNS 名称，因为它是用于对容器或其 Blob 寻址的唯一 URI 的一部分。 为容器命名时，请遵循以下规则：

- 容器名称的长度可以是 3 到 63 个字符。
- 容器名称必须以字母或数字开头，并且只能包含小写字母、数字和短划线 (-) 字符。
- 容器名称中不允许出现两个或更多个连续的短划线字符。

容器的 URI 采用以下格式：

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>创建容器

若要创建容器，请调用以下方法之一：

- [创建](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

如果已存在同名的容器，**Create** 和 **CreateAsync** 方法将引发异常。

**CreateIfNotExists** 和 **CreateIfNotExistsAsync** 方法返回一个指示是否已创建容器的布尔值。 如果已存在同名的容器，这些方法将返回 **False**，指示未创建新容器。

将立即在存储帐户下创建容器。 无法将一个容器嵌套在另一个容器下。

以下示例以异步方式创建一个容器：

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

根容器充当存储帐户的默认容器。 每个存储帐户只能包含一个根容器，该容器必须命名为 *$root.* 。 必须显式创建或删除根容器。

可以引用存储在根容器中的 Blob，而无需包含根容器名称。 根容器允许引用位于存储帐户层次结构顶层的 Blob。 例如，可通过以下方式引用驻留在根容器中的 Blob：

`https://myaccount.blob.core.windows.net/default.html`

以下示例以同步方式创建根容器：

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
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

如果该容器不存在，**Delete** 和 **DeleteAsync** 方法将引发异常。

**DeleteIfNotExists** 和 **DeleteIfNotExistsAsync** 方法返回一个指示是否已删除容器的布尔值。 如果指定的容器不存在，则这些方法将返回 **False**，指示未删除该容器。

删除容器后，至少在 30 秒（时间可能更长）内无法使用相同的名称创建容器。 正在删除容器时，尝试使用相同的名称创建容器将会失败，并出现 HTTP 错误代码 409（冲突）。 正在删除容器时，针对容器或其包含的 Blob 执行任何其他操作将会失败，并出现 HTTP 错误代码 404（未找到）。

以下示例删除指定的容器，并在该容器不存在时处理异常：

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

以下示例演示如何删除以指定的前缀开头的所有容器。 如果该容器中存在租约，该示例将中断租约。

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

## <a name="see-also"></a>请参阅

- [Create Container 操作](/rest/api/storageservices/create-container)
- [Delete Container 操作](/rest/api/storageservices/delete-container)
