---
title: 安全访问应用程序数据
titleSuffix: Azure Storage
description: 使用 SAS 令牌、加密和 HTTPS 保护云中的应用程序数据。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: b027ed6b936761e35e835401f9ce8398fac33073
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129636"
---
# <a name="secure-access-to-application-data"></a>安全访问应用程序数据

本教程是一个系列中的第三部分。 你将了解如何安全访问存储帐户。 

在该系列的第三部分中，你会学习如何：

> [!div class="checklist"]
> * 使用 SAS 令牌访问缩略图图像
> * 启用服务器端加密
> * 启用仅 HTTPS 传输

[Azure blob 存储](../common/storage-introduction.md#blob-storage)提供存储应用程序文件的可靠服务。 本教程对[上一个主题][previous-tutorial]进行扩展，演示如何从 Web 应用程序安全访问存储帐户。 完成后，图像会被加密，Web 应用使用安全的 SAS 令牌访问缩略图图像。

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须先完成上一存储教程：[使用事件网格自动调整上传图像的大小][previous-tutorial]。

## <a name="set-container-public-access"></a>设置容器公共访问权限

在系列教程的此部分中，SAS 令牌用于访问缩略图。 在此步骤中，请将 *thumbnails* 容器的公共访问权限设置为 `off`。

```azurecli-interactive 
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>为缩略图配置 SAS 令牌

在本系列教程的第一部分中，Web 应用程序显示公共容器中的图像。 在此系列教程的这个部分中，请使用共享访问签名 (SAS) 令牌检索缩略图图像。 通过 SAS 令牌，可以实现基于 IP、协议、时间间隔或允许的权限提供对容器或 blob 的受限访问权限。 有关 SAS 的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。

此示例中，源代码存储库使用 `sasTokens` 分支，该分支有更新的代码示例。 使用 [az webapp deployment source delete](/cli/azure/webapp/deployment/source) 删除现有的 GitHub 部署。 然后，使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source) 命令配置 Web 应用的 GitHub 部署。

在下面的命令中，`<web-app>` 是 Web 应用的名称。

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

存储库的 `sasTokens` 分支更新了 `StorageHelper.cs` 文件。 它将使用下面的代码示例替换 `GetThumbNailUrls` 任务。 已更新的任务通过使用 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 指定 SAS 令牌的开始时间、到期时间和权限来检索缩略图 URL。 部署 Web 应用后，使用 SAS 令牌检索带 URL 的缩略图。 以下示例显示已更新任务：

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

以下是用于前一任务的类、属性和方法：

| 类 | 属性 | 方法 |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [名称](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [查询](/dotnet/api/system.uribuilder.query) |  |
|[列表](/dotnet/api/system.collections.generic.list-1) | | [添加](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="server-side-encryption"></a>服务器端加密

[Azure 存储服务加密 (SSE)](../common/storage-service-encryption.md) 可帮助你保护数据。 SSE 加密静态数据，处理加密、解密和密钥管理。 采用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)所有数据，它是现在最强有力的分组密码之一。

SSE 自动加密所有性能层（标准和高级）、所有部署模型（Azure 资源管理器和经典）、所有 Azure 存储服务（Blob、队列、表和文件）中的数据。 

## <a name="enable-https-only"></a>仅启用 HTTPS

为了确保对存储帐户数据的请求安全，可以将请求限制为仅 HTTPS。 使用 [az storage account update](/cli/azure/storage/account) 命令，更新协议所需的存储帐户。

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

通过使用 `HTTP` 协议的 `curl` 测试连接。

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

由于需要安全的传输，将收到以下消息：

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>后续步骤

本系列教程的第三部分介绍了如何安全访问存储帐户，例如如何：

> [!div class="checklist"]
> * 使用 SAS 令牌访问缩略图图像
> * 启用服务器端加密
> * 启用仅 HTTPS 传输

本系列教程第四部分介绍如何对云存储应用程序进行监视和故障排除。

> [!div class="nextstepaction"]
> [对云存储应用程序进行监视和故障排除](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
