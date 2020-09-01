---
title: 启用和管理 blob 版本控制
titleSuffix: Azure Storage
description: 了解如何在 Azure 门户中或使用 Azure 资源管理器模板启用 blob 版本控制。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 1df7afb5a029ff7770a64d6bf698a462c8ab9735
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230664"
---
# <a name="enable-and-manage-blob-versioning"></a>启用和管理 blob 版本控制

可以启用 Blob 存储版本控制来自动维护对象的以前版本。  启用 blob 版本控制后，如果错误地修改或删除了数据，则可以还原该 blob 的早期版本以恢复数据。

本文介绍如何使用 Azure 门户或 Azure 资源管理器模板启用或禁用存储帐户的 blob 版本控制。 若要了解有关 blob 版本控制的详细信息，请参阅 [blob 版本控制](versioning-overview.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>启用 Blob 版本控制

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

在 Azure 门户中启用 blob 版本控制：

1. 导航到门户中的存储帐户。
1. 在 " **Blob 服务**" 下，选择 " **数据保护**"。
1. 在 " **版本控制** " 部分，选择 " **已启用**"。

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="显示如何在 Azure 门户中启用 blob 版本控制的屏幕截图":::

# <a name="template"></a>[模板](#tab/template)

若要启用具有模板的 blob 版本控制，请创建一个模板，并将 **IsVersioningEnabled** 属性 **设置为 true**。 以下步骤介绍如何在 Azure 门户中创建模板。

1. 在 Azure 门户中，选择“创建资源”。
1. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
1. 选择 " **模板部署**"，选择 " **创建**"，然后选择 **"在编辑器中生成自己的模板"**。
1. 在模板编辑器中，粘贴以下 JSON。 将 `<accountName>` 占位符替换为存储帐户的名称。
1. 保存模板。
1. 指定帐户的资源组，然后选择 " **购买** " 按钮部署模板并启用 blob 版本控制。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

有关 Azure 门户中的模板部署资源的详细信息，请参阅 [部署具有 Azure 门户的资源](../../azure-resource-manager/templates/deploy-portal.md)。

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>修改 blob 以触发新版本

下面的代码示例演示如何使用适用于 .NET 的 Azure 存储客户端库版本 [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 或更高版本触发新版本的创建。 运行此示例之前，请确保已为存储帐户启用了版本控制。

该示例创建一个块 blob，然后更新该 blob 的元数据。 更新 blob 的元数据会触发新版本的创建。 该示例将检索初始版本和当前版本，并显示只有当前版本包括元数据。

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>后续步骤

- [Blob 版本控制](versioning-overview.md)
- [Azure 存储 Blob 的软删除](soft-delete-overview.md)
