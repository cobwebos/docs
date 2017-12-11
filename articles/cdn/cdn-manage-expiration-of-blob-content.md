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
ms.openlocfilehash: 694d0c27b26c1ed9f6a1a54f766d024d882b5b64
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>在 Azure 内容交付网络中管理 Azure Blob 存储的到期时间
> [!div class="op_single_selector"]
> * [Azure Web 内容](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 存储](cdn-manage-expiration-of-blob-content.md)
> 
> 

在多个与 Azure 内容交付网络 (CDN) 集成的基于 Azure 的源中，Azure 存储中的 [Blob 存储服务](../storage/common/storage-introduction.md#blob-storage)是其中一个。 任何可公开访问的 blob 内容均可在 Azure CDN 中进行缓存，直到其生存时间 (TTL) 结束。 TTL 由来自源服务器的 HTTP 响应中的 `Cache-Control` 标头决定。 本文介绍了几种可以在 Azure 存储中的 Blob 上设置 `Cache-Control` 标头的方式。

此外，还可以通过设置 [CDN 缓存规则](cdn-caching-rules.md)从 Azure 门户控制缓存设置。 如果已设置了一个或多个缓存规则并已将其缓存行为设置为“替代”或“绕过缓存”，则将忽略本文中讨论的源提供的缓存设置。 有关一般缓存概念的信息，请参阅[缓存工作原理](cdn-how-caching-works.md)。

> [!TIP]
> 可以选择不对 blob 设置 TTL。 在这种情况下，Azure CDN 将自动应用默认 TTL（七天），除非已在 Azure 门户中设置了缓存规则。 此默认 TTL 仅适用于常规 Web 交付优化。 对于大型文件优化，默认 TTL 为一天；对于媒体流优化，默认 TTL 为一年。
> 
> 有关 Azure CDN 如何加速访问 blob 和其他文件的详细信息，请参阅 [Azure 内容交付网络概述](cdn-overview.md)。
> 
> 有关 Azure Blob 存储的详细信息，请参阅 [Blob 存储简介](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)。
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>使用 Azure PowerShell 设置 Cache-Control 标头
[Azure PowerShell](/powershell/azure/overview) 是管理 Azure 服务的最快捷且最强大的方式之一。 使用 `Get-AzureStorageBlob`cmdlet 来获取对 blob 的引用，并设置 `.ICloudBlob.Properties.CacheControl` 属性。 

例如：

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> 还可以使用 PowerShell 来[管理 CDN 配置文件和终结点](cdn-manage-powershell.md)。
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>使用 .NET 设置 Cache-Control 标头
要使用 .NET 代码设置 Blob 的 `Cache-Control` 标头，请使用[适用于 .NET 的 Azure 存储客户端库](../storage/blobs/storage-dotnet-how-to-use-blobs.md)来设置 [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) 属性。

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
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> 更多 .NET 代码示例可在[适用于.NET 的 Azure Blob 存储示例](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)中找到。
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>使用其他方法设置 Cache-Control 标头

### <a name="azure-storage-explorer"></a>Azure 存储资源管理器
借助 [Azure 存储资源管理器](https://azure.microsoft.com/en-us/features/storage-explorer/)，可以查看和编辑 Blob 存储资源，包括 CacheControl 等属性。 

若要使用 Azure 存储资源管理器更新 blob 的 *CacheControl* 属性：
   1. 选择 blob，然后从上下文菜单中选择“属性”。 
   2. 向下滚动到 *CacheControl* 属性。
   3. 输入一个值，然后单击“保存”。


![Azure 存储资源管理器属性](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure 命令行接口
使用 [Azure 命令行接口](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) (CLI)，可以从命令行管理 Azure blob 资源。 若要在使用 Azure CLI 上传 Blob 时设置 cache-control 标头，可使用 `-p` 开关设置 *cacheControl* 属性。 以下示例说明如何将 TTL 设置为 1 小时（3600 秒）：
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\test.txt myContainer test.txt
```

### <a name="azure-storage-services-rest-api"></a>Azure 存储空间服务 REST API
通过对请求执行以下操作，可以使用 [Azure 存储服务 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 来显式设置 *x-ms-blob-cache-control* 属性：
  
   - [放置 Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [放置块列表](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [设置 Blob 属性](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>测试 Cache-Control 标头
可以轻松验证 Blob 的 TTL 设置。 使用浏览器的[开发人员工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)，测试 Blob 是否包含 `Cache-Control` 响应标头。 还可使用 [Wget](https://www.gnu.org/software/wget/)、[Postman](https://www.getpostman.com/) 或 [Fiddler](http://www.telerik.com/fiddler) 等工具检查响应标头。

## <a name="next-steps"></a>后续步骤
* [了解如何管理 Azure CDN 中云服务内容的过期问题](cdn-manage-expiration-of-cloud-service-content.md)
* [了解缓存概念](cdn-how-caching-works.md)

