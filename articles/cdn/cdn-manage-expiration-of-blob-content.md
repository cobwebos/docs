---
title: "在 Azure 内容交付网络中管理 Azure Blob 存储的到期时间 | Microsoft Docs"
description: "了解用于控制 Azure CDN 缓存中 blob 生存时间的选项。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: c2b49058ec7dd52b5063e815447697fa17ddb53a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>在 Azure 内容交付网络中管理 Azure Blob 存储的到期时间
> [!div class="op_single_selector"]
> * [Azure Web 内容](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 存储](cdn-manage-expiration-of-blob-content.md)
> 
> 

在多个与 Azure 内容交付网络 (CDN) 集成的基于 Azure 的源中，Azure 存储中的 [Blob 存储服务](../storage/common/storage-introduction.md#blob-storage)是其中一个。 任何可公开访问的 blob 内容均可在 Azure CDN 中进行缓存，直到其生存时间 (TTL) 结束。 TTL 由来自源服务器的 HTTP 响应中的 `Cache-Control` 标头决定。 本文介绍了几种可以在 Azure 存储中的 Blob 上设置 `Cache-Control` 标头的方式。

> [!TIP]
> 可以选择不对 blob 设置 TTL。 在这种情况下，Azure CDN 会自动应用默认为 7 天的 TTL。
> 
> 有关 Azure CDN 如何加速访问 blob 和其他文件的详细信息，请参阅 [Azure 内容交付网络概述](cdn-overview.md)。
> 
> 有关 Azure Blob 存储的详细信息，请参阅 [Blob 存储简介](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)。
 

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) 是管理 Azure 服务的最快捷且最强大的方式之一。 使用 `Get-AzureStorageBlob`cmdlet 来获取对 blob 的引用，并设置 `.ICloudBlob.Properties.CacheControl` 属性。 

例如：

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> 还可以使用 PowerShell 来[管理 CDN 配置文件和终结点](cdn-manage-powershell.md)。
> 
>

## <a name="azure-storage-client-library-for-net"></a>适用于 .NET 的 Azure 存储客户端库
要使用 .NET 设置 Blob 的 `Cache-Control` 标头，请使用[适用于 .NET 的 Azure 存储客户端库](../storage/blobs/storage-dotnet-how-to-use-blobs.md)来设置 [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) 属性。

例如：

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> 更多 .NET 代码示例可在[适用于.NET 的 Azure Blob 存储示例](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)中找到。
> 
> 

## <a name="other-methods"></a>其他方法
* [Azure 命令行界面](../cli-install-nodejs.md)
  
    上传 Blob 时，可以在 Azure 命令行界面中使用 `-p` 开关设置 *cacheControl* 属性。 以下示例将 TTL 设置为 1 小时（3600 秒）：
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [Azure 存储空间服务 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    针对[放置 Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)、[放置块列表](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)或 [Blob 属性](https://msdn.microsoft.com/library/azure/ee691966.aspx)请求上，显式设置 *x-ms-blob-cache-control* 的属性。

* 第三方存储管理工具
  
    使用某些第三方 Azure 存储管理工具可以设置 Blob 的 **CacheControl** 属性。 

## <a name="testing-the-cache-control-header"></a>测试 Cache-Control 标头
可以轻松验证 Blob 的 TTL 设置。 使用浏览器的[开发人员工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)，测试 Blob 是否包含 `Cache-Control` 响应标头。 还可以使用 **wget**、[Postman](https://www.getpostman.com/) 或 [Fiddler](http://www.telerik.com/fiddler) 等工具检查响应标头。

## <a name="next-steps"></a>后续步骤
* [了解如何管理 Azure CDN 中云服务内容的过期问题](cdn-manage-expiration-of-cloud-service-content.md)

