---
title: "使用 Azure 存储安全访问云中的应用程序数据 |Microsoft Docs"
description: "使用 SAS 令牌、加密和 HTTPS 保护云中的应用程序数据"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 11b141617bea5962c45e1b91cf6629c2edd26c3d
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>安全访问云中的应用程序数据

本教程是一个系列中的第三部分。 你将了解如何安全访问存储帐户。 

在该系列的第三部分中，你会学习如何：

> [!div class="checklist"]
> * 使用 SAS 令牌访问缩略图图像
> * 启用服务器端加密
> * 启用仅 HTTPS 传输

[Azure blob 存储](../common/storage-introduction.md#blob-storage)提供存储应用程序文件的可靠服务。 此教程对[上一个主题][previous-tutorial]进行扩展，演示如何从 Web 应用程序安全访问存储帐户。 完成后，图像会被加密，Web 应用使用安全的 SAS 令牌访问缩略图图像。

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须先完成以前的“存储”教程：[使用事件网格自动调整上传图像的大小][previous-tutorial]。 

## <a name="set-container-public-access"></a>设置容器公共访问权限

在系列教程的此部分中，SAS 令牌用于访问缩略图。 在此步骤中，将 thumbs 容器的公共访问权限设置为“`off`”。

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>为缩略图配置 SAS 令牌

在本系列教程的第一部分中，Web 应用程序显示公共容器中的图像。 在系列教程的此部分中，使用[安全访问签名 (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature)令牌检索缩略图图像。 通过 SAS 令牌，可以实现基于 IP、协议、时间间隔或允许的权限提供对容器或 blob 的受限访问权限。

此示例中，源代码存储库使用 `sasTokens` 分支，该分支有更新的代码示例。 使用 [az webapp deployment source delete](/cli/azure/webapp/deployments/source#delete) 删除现有的 GitHub 部署。 然后，使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source#config) 命令配置 Web 应用的 GitHub 部署。  

在下面的命令中，`<web-app>` 是 Web 应用的名称。  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

存储库的 `sasTokens` 分支更新了 `StorageHelper.cs` 文件。 它将使用下面的代码示例替换 `GetThumbNailUrls` 任务。 通过设置 [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) 指定 SAS 令牌的开始时间、到期时间和权限，更新的任务会检索缩略图 URL。 部署 Web 应用后，使用 SAS 令牌检索带 URL 的缩略图。 以下示例显示已更新任务：
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

以下是用于前一任务的类、属性和方法：

|类  |属性| 方法  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [结果](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[权限](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>服务器端加密

[Azure 存储服务加密 (SSE)](../common/storage-service-encryption.md) 可帮助你保护数据。 SSE 加密静态数据，处理加密、解密和密钥管理。 采用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)所有数据，它是现在最强有力的分组密码之一。

下面的示例中会启用 blob 加密。 启用加密前创建的现有 blob 未加密。 blob 的请求上的 `x-ms-server-encrypted` 标头显示 blob 的加密状态。

```azurecli-interactive
az storage account update --encryption-services blob --name <storage-account-name> --resource-group myResourceGroup
```

由于已启用加密，新图像上传到 Web 应用程序。

使用 `curl` 和开关 `-I` 仅检索标头，将 `<storage-account-name>`、`<container>` 和 `<blob-name>` 替换为自己的值。  

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<blob-name>?$sasToken -I
```

在响应中，请注意 `x-ms-server-encrypted` 标头显示 `true`。 此标头确定现在数据使用 SSE 加密。

```
HTTP/1.1 200 OK
Content-Length: 209489
Content-Type: image/png
Last-Modified: Mon, 11 Sep 2017 19:27:42 GMT
Accept-Ranges: bytes
ETag: "0x8D4F94B2BE76D45"
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 57047db3-001e-0050-3e34-2ba769000000
x-ms-version: 2017-04-17
x-ms-lease-status: unlocked
x-ms-lease-state: available
x-ms-blob-type: BlockBlob
x-ms-server-encrypted: true
Date: Mon, 11 Sep 2017 19:27:46 GMT
```

## <a name="enable-https-only"></a>仅启用 HTTPS

为了确保对存储帐户数据的请求安全，可以将请求限制为仅 HTTPS。 使用 [az storage account update](/cli/azure/storage/account#update) 命令，更新协议所需的存储帐户。

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
